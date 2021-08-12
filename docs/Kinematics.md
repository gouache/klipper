# 운동학

이 문서는 Klipper가 로봇 모션을 구현하는 방법에 대한 개요를 제공합니다 ([운동학](https://en.wikipedia.org/wiki/Kinematics)).
아래 내용은 Klipper 소프트웨어 작업에 관심이 있는 개발자와 기계의 역학을 더 잘 이해하는데 관심이 있는 사용자 모두에게 흥미로울 수 있습니다.

## 가속도

Klipper는 프린트 헤드가 속도를 변경할 때마다 일정한 가속을 합니다. 속도는 갑자기 급상승하지 않고 점진적으로 새로운 속도로 변경됩니다. Klipper는 항상 툴헤드와 인쇄물 사이에 가속을 적용합니다. 익스트루더를 떠나는 필라멘트는 매우 약할 수 있기 때문에 급격한 저크 및/또는 압출기 흐름 변화는 출력물의 품질과 베드 접착력을 저하시킵니다. 압출되지 않은 상태에서도 프린트 헤드가 프린트와 같은 높이에 있으면 헤드의 급격한 요동으로 최근에 출력된 필라멘트가 손싱될 수 있습니다. 이 때 프린트 헤드의 속도 변경을 제한하면 출력이 실패할 위험이 줄어듭니다.

스테퍼 모터가 탈조하거나 기계에 과도한 스트레스를 주지 않도록 가속을 제한하는 것도 중요합니다. Klipper는 프린트 헤드의 가속을 제한함으로써 각 스테퍼의 토크를 제한합니다.
프린트 헤드에 가속을 적용하면 프린트 헤드를 움직이는 스테퍼의 토크도 자연스럽게 제한됩니다(반대의 경우가 항상 있는 것은 아닙니다).

Klipper는 일정한 가속을 구현합니다. 등가속도의 핵심 공식은 다음과 같습니다:
```
velocity(time) = start_velocity + accel*time
```

## 사다리꼴 생성기

Klipper는 기존의 "사다리꼴 생성기"를 사용하여 각 동작의 동작을 모델링합니다. 각 동작에는 시작 속도가 있고, 일정한 가속에서 순항 속도로 가속하고, 일정한 속도로 순항한 다음, 일정한 가속을 사용하여 끝 속도로 감속합니다.

![사다리꼴](img/trapezoid.svg.png)

움직임의 속도 다이어그램이 사다리꼴처럼 보이기 때문에 이것을 "사다리꼴 생성기"라고 합니다.

순항 속도는 항상 시작 속도와 끝 속도보다 크거나 같습니다. 가속 단계는 지속 시간이 0일 수 있고(시작 속도가 순항 속도와 동일한 경우) 순항 단계는 지속 시간이 0일 수 있으며(가속 후 즉시 감속이 시작되는 경우), 감속 단계는 0일 수 있습니다. 지속 시간(종료 속도가 순항 속도와 동일한 경우).

![사다리꼴](img/trapezoids.svg.png)

## 예측 (look-ahead)

"예측" 시스템은 움직임 사이의 코너링 속도를 결정하는 데 사용됩니다.

XY 평면에서 일어날 수 있는 다음 두 가지 이동을 생각해 보십시오:

![코너](img/corner.svg.png)

위의 상황에서 첫 번째 이동 후에 완전히 감속한 후 다음 이동을 시작할 때 완전히 가속하는 것도 가능하지만, 이런 모든 가속과 감속이 출력 시간을 크게 증가시키고 압출기 흐름의 빈번한 변화를 증가시키므로 이상적이지 않습니다. 또한 이로인해 인쇄 품질도 떨어질 수 있습니다.

이를 해결하기 위해 "예측" 메커니즘은 들어오는 여러 움직임을 대기열에 넣고 움직임 사이의 각도를 분석하여 두 움직임 사이의 "연결" 동안 얻을 수 있는 합리적인 속도를 결정합니다. 다음 움직임이 거의 같은 방향에 있으면 해드만 약간 느려지면 됩니다.

![예측](img/lookahead.svg.png)

그러나 다음 이동이 예각을 형성하는 경우(헤드가 다음 이동에서 거의 반대 방향으로 이동하게 됨) 작은 연결 속도만 허용됩니다.

![예측](img/lookahead-slow.svg.png)

연결 속도는 "approximated centripetal acceleration"를 사용하여 결정됩니다. [저자 설명](https://onehossshay.wordpress.com/2011/09/24/improving_grbl_cornering_algorithm/)을 참고. 그러나 Klipper에서 연결 속도는 90° 모서리가 가져야 하는 원하는 속도("정사각형 모서리 속도")를 지정하여 구성되며 다른 각도에 대한 접합 속도는 여기서 파생됩니다.

예측을 위한 핵심 공식:
```
end_velocity^2 = start_velocity^2 + 2*accel*move_distance
```

### 부드러운 예측 (Smoothed Look-ahed)

Klipper는 또한 짧은 "지그재그" 동작을 부드럽게 하는 메커니즘을 구현합니다. 다음 동작을 살펴보세요.

![zigzag](img/zigzag.svg.png)

위의 경우 가속에서 감속으로의 빈번한 변화는 기계를 진동시켜 기계에 스트레스를 일으키고 소음을 증가시킬 수 있습니다.이를 줄이기 위해 Klipper는 일반적인 이동 가속과 가상 "가속에서 감속" 비율을 모두 추적합니다. 이 시스템을 사용하면 이러한 짧은 "지그재그" 이동의 최고 속도는 프린터 동작을 부드럽게 하기 위해 제한됩니다:

![smoothed](img/smoothed.svg.png)

특히 코드는 이 가상 "가감속 가속" 비율 (기본적으로 정상 가속 비율의 절반)로 제한되는 경우 각 이동의 속도를 계산합니다. 위의 그림에서 회색 점선은 첫 번째 이동에 대한 가상 가속도를 나타냅니다. 이동이 이 가상 가속도를 사용하여 최대 순항 속도에 도달할 수 없는 경우 최고 속도는 이 가상 가속도에서 얻을 수 있는 최대 속도로 감소됩니다. 대부분의 동작에 대해 제한은 이동의 기존 제한 이상이며 동작의 변화가 생기지 않습니다. 그러나 짧은 지그재그 이동의 경우 이 제한으로 인해 최고 속도가 감소합니다. 이동 내 실제 가속은 변경되지 않습니다. 이동은 조정된 최고 속도까지 일반 가속 계획을 계속 사용합니다.

## Generating steps

Once the look-ahead process completes, the print head movement for the
given move is fully known (time, start position, end position,
velocity at each point) and it is possible to generate the step times
for the move. This process is done within "kinematic classes" in the
Klipper code. Outside of these kinematic classes, everything is
tracked in millimeters, seconds, and in cartesian coordinate space.
It's the task of the kinematic classes to convert from this generic
coordinate system to the hardware specifics of the particular printer.

Klipper uses an
[iterative solver](https://en.wikipedia.org/wiki/Root-finding_algorithm)
to generate the step times for each stepper. The code contains the
formulas to calculate the ideal cartesian coordinates of the head at
each moment in time, and it has the kinematic formulas to calculate
the ideal stepper positions based on those cartesian coordinates. With
these formulas, Klipper can determine the ideal time that the stepper
should be at each step position. The given steps are then scheduled at
these calculated times.

The key formula to determine how far a move should travel under
constant acceleration is:
```
move_distance = (start_velocity + .5 * accel * move_time) * move_time
```
and the key formula for movement with constant velocity is:
```
move_distance = cruise_velocity * move_time
```

The key formulas for determining the cartesian coordinate of a move
given a move distance is:
```
cartesian_x_position = start_x + move_distance * total_x_movement / total_movement
cartesian_y_position = start_y + move_distance * total_y_movement / total_movement
cartesian_z_position = start_z + move_distance * total_z_movement / total_movement
```

### 카르테시안 로봇

Generating steps for cartesian printers is the simplest case. The
movement on each axis is directly related to the movement in cartesian
space.

Key formulas:
```
stepper_x_position = cartesian_x_position
stepper_y_position = cartesian_y_position
stepper_z_position = cartesian_z_position
```

### 코어XY 로봇

Generating steps on a CoreXY machine is only a little more complex
than basic cartesian robots. The key formulas are:
```
stepper_a_position = cartesian_x_position + cartesian_y_position
stepper_b_position = cartesian_x_position - cartesian_y_position
stepper_z_position = cartesian_z_position
```

### 델타 로봇

Step generation on a delta robot is based on Pythagoras's theorem:
```
stepper_position = (sqrt(arm_length^2
                         - (cartesian_x_position - tower_x_position)^2
                         - (cartesian_y_position - tower_y_position)^2)
                    + cartesian_z_position)
```

### 스테퍼 모터 가속 제한

With delta kinematics it is possible for a move that is accelerating
in cartesian space to require an acceleration on a particular stepper
motor greater than the move's acceleration. This can occur when a
stepper arm is more horizontal than vertical and the line of movement
passes near that stepper's tower. Although these moves could require a
stepper motor acceleration greater than the printer's maximum
configured move acceleration, the effective mass moved by that stepper
would be smaller. Thus the higher stepper acceleration does not result
in significantly higher stepper torque and it is therefore considered
harmless.

However, to avoid extreme cases, Klipper enforces a maximum ceiling on
stepper acceleration of three times the printer's configured maximum
move acceleration. (Similarly, the maximum velocity of the stepper is
limited to three times the maximum move velocity.) In order to enforce
this limit, moves at the extreme edge of the build envelope (where a
stepper arm may be nearly horizontal) will have a lower maximum
acceleration and velocity.

### 익스트루더 운동학

Klipper implements extruder motion in its own kinematic class. Since
the timing and speed of each print head movement is fully known for
each move, it's possible to calculate the step times for the extruder
independently from the step time calculations of the print head
movement.

Basic extruder movement is simple to calculate. The step time
generation uses the same formulas that cartesian robots use:
```
stepper_position = requested_e_position
```

### 압력 조절 (Pressure advance)

Experimentation has shown that it's possible to improve the modeling
of the extruder beyond the basic extruder formula. In the ideal case,
as an extrusion move progresses, the same volume of filament should be
deposited at each point along the move and there should be no volume
extruded after the move. Unfortunately, it's common to find that the
basic extrusion formulas cause too little filament to exit the
extruder at the start of extrusion moves and for excess filament to
extrude after extrusion ends. This is often referred to as "ooze".

![ooze](img/ooze.svg.png)

The "pressure advance" system attempts to account for this by using a
different model for the extruder. Instead of naively believing that
each mm^3 of filament fed into the extruder will result in that amount
of mm^3 immediately exiting the extruder, it uses a model based on
pressure. Pressure increases when filament is pushed into the extruder
(as in [Hooke's law](https://en.wikipedia.org/wiki/Hooke%27s_law)) and
the pressure necessary to extrude is dominated by the flow rate
through the nozzle orifice (as in
[Poiseuille's law](https://en.wikipedia.org/wiki/Poiseuille_law)). The
key idea is that the relationship between filament, pressure, and flow
rate can be modeled using a linear coefficient:
```
pa_position = nominal_position + pressure_advance_coefficient * nominal_velocity
```

See the [pressure advance](Pressure_Advance.md) document for
information on how to find this pressure advance coefficient.

The basic pressure advance formula can cause the extruder motor to
make sudden velocity changes. Klipper implements "smoothing" of the
extruder movement to avoid this.

![pressure-advance](img/pressure-velocity.png)

The above graph shows an example of two extrusion moves with a
non-zero cornering velocity between them. Note that the pressure
advance system causes additional filament to be pushed into the
extruder during acceleration. The higher the desired filament flow
rate, the more filament must be pushed in during acceleration to
account for pressure. During head deceleration the extra filament is
retracted (the extruder will have a negative velocity).

The "smoothing" is implemented using a weighted average of the
extruder position over a small time period (as specified by the
`pressure_advance_smooth_time` config parameter). This averaging can
span multiple g-code moves. Note how the extruder motor will start
moving prior to the nominal start of the first extrusion move and will
continue to move after the nominal end of the last extrusion move.

Key formula for "smoothed pressure advance":
```
smooth_pa_position(t) =
    ( definitive_integral(pa_position(x) * (smooth_time/2 - abs(t - x)) * dx,
                          from=t-smooth_time/2, to=t+smooth_time/2)
     / (smooth_time/2)^2 )
```
