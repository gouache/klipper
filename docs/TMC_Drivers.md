# TMC drivers

이 문서는 Klipper의 SPI/UART 모드에서 Trinamic 스테퍼 모터 드라이버를 사용하는 
방법에 대한 정보를 제공합니다.

Klipper는 "standalone mode"에서 Trinamic 드라이버를 사용할 수도 있습니다. 
그러나 드라이버가 이 모드에 있으면 특별한 Klipper 구성이 필요하지 않으며 
이 문서에서 설명하는 고급 Klipper 기능을 사용할 수 없습니다.

이 문서 외에 [TMC 드라이버 구성 참조](Config_Reference.md#tmc-stepper-driver-configuration)를 반드시 검토하십시오.

## "Stealthchop" 모드 활성화

기본적으로 Klipper는 TMC 드라이버를 "spreadcycle" 모드로 설정합니다. 
드라이버가 "stealthchop"을 지원하는 경우 TMC 구성 섹션에 
`stealthchop_threshold: 999999`를 추가하여 활성화할 수 있습니다.

항상 "spreadcycle" 모드(`stealthchop_threshold`를 지정하지 않음)를 사용하거나 
항상 `stealthchop_threshold`를 999999로 설정하여 `stealthchop` 모드를 사용하는 
것이 좋습니다. 불행히도, 모터가 0이 아닌 속도에 있는 동안 모드가 변경되면 
드라이버는 종종 좋지 않고 혼란스러운 결과를 생성합니다.

## 센서리스 원점복귀

센서리스 원점복귀는 물리적 리미트 스위치 없이도 축을 원점복귀할 수 있습니다. 
대신 축의 캐리지가 기계적 한계지점으로 이동되어 스테퍼 모터가 step제어가 풀리게 됩니다.
스테퍼 드라이버는 손실된 단계를 감지하고 핀을 토글하여 제어 MCU(Klipper)에 이를 
표시합니다. 이 정보는 Klipper에서 축의 끝점으로 사용할 수 있습니다.

이 가이드는 (직교) 프린터의 X 축에 대한 센서리스 원점 복귀 설정을 다룹니다. 
그러나 다른 모든 축(끝 정지가 필요한 축)과 동일하게 작동합니다. 
한 번에 하나의 축에 대해 구성하고 조정해야 합니다.

### 제한사항

기계 구성 요소가 축의 한계에 반복적으로 부딪치는 캐리지의 하중을 처리할 수 
있는지 확인하십시오. 특히 리드스크류는 많은 힘을 발생시킬 수 있습니다. 
노즐을 인쇄 표면에 부딪혀 Z축의 원점 복귀는 좋은 생각이 아닐 수 있습니다. 
최상의 결과를 얻으려면 축 캐리지가 축 제한에 단단히 닿는지 확인하십시오.

또한 센서리스 원점 복귀는 프린터에 대해 충분히 정확하지 않을 수 있습니다. 
직교 기계에서 X 및 Y 축의 귀환은 잘 작동할 수 있지만 Z 축의 귀환은 일반적으로 
충분히 정확하지 않으며 일관되지 않은 첫 번째 레이어 높이를 초래할 수 있습니다. 
센서가 없는 델타 프린터의 원점 복귀는 정확도가 떨어지므로 권장하지 않습니다.

또한 스테퍼 드라이버의 stall 감지는 모터의 기계적 부하, 모터 전류 및 모터 
온도(코일 저항)에 따라 달라집니다.

센서리스 원점 복귀는 모터의 적절한 속도에서 가장 잘 작동합니다. 
매우 느린 속도(10RPM 미만)의 경우 모터는 상당한 역기전력을 생성하지 않으며 
TMC는 모터 stall을 안정적으로 감지할 수 없습니다. 또한 매우 빠른 속도에서 
모터의 역기전력이 모터의 공급 전압에 접근하므로 TMC는 더 이상 stall을 감지할
수 없습니다. TMC의 데이터시트를 살펴보는 것이 좋습니다. 여기에서 
이 설정의 제한 사항에 대한 자세한 내용도 찾을 수 있습니다.

### 전제조건

센서리스 원점 복귀를 사용하려면 몇 가지 전제 조건이 필요합니다:

1. StallGuard 지원 TMC 스테퍼 드라이버(tmc2130, tmc2209, tmc2660 또는 tmc5160).
2. 마이크로 컨트롤러에 연결된 TMC 드라이버의 SPI/UART 인터페이스
   (stand-alone 모드는 작동하지 않음).
3. 마이크로 컨트롤러에 연결된 TMC 드라이버의 적절한 "DIAG" 또는 "SG_TST" 핀.
4. [구성 검사](Config_checks.md) 문서의 단계를 실행하여 스테퍼 모터가 구성되고 제대로 작동하는지 확인해야 합니다.

### 조정

여기에 설명된 절차에는 6가지 주요 단계가 있습니다:
1. 원점 복귀 속도를 선택합니다.
2. 센서리스 원점 복귀를 활성화하도록 `printer.cfg` 파일을 구성합니다.
3. 성공적으로 홈에 도달하는 가장 높은 민감도의 stallguard 설정을 찾으십시오.
4. 한번의 터치로 성공적으로 홈으로 돌아가는 최저 민감도의 
   stallguard 설정을 찾으십시오.
5. 원하는 stallguard 설정으로 `printer.cfg`를 업데이트하십시오.
6. 일관되게 홈에 대한 `printer.cfg` 매크로를 생성하거나 업데이트합니다.

#### 원점복귀 속도 선택

원점복귀 속도는 센서리스 원점복귀를 수행할 때 중요한 선택입니다. 
레일 끝부분에 닿을 때 캐리지가 프레임에 과도한 힘을 가하지 않도록 
느린 원점복귀 속도를 사용하는 것이 바람직합니다. 그러나 TMC 드라이버는 
매우 느린 속도에서는 stall을 안정적으로 감지할 수 없습니다.

원점복귀 속도의 좋은 시작점은 스테퍼 모터가 2초마다 완전히 회전하는 것입니다. 
많은 축의 경우 이는 `rotation_distance`를 2로 나눈 값입니다. 예를 들어:

```
[stepper_x]
rotation_distance: 40
homing_speed: 20
...
```

#### 센서리스 원점복귀를 위해 printer.cfg 구성

두 번째 원점복귀 이동을 비활성화하려면 `stepper_x` 구성 섹션에서 
`homing_retract_dist` 설정은 0으로 설정해야 합니다. 두 번째 원점복귀 시도는 
센서리스 원점복귀를 사용할 때 설정값을 추가하지 않으며 안정적으로 
작동하지 않으며 튜닝 프로세스를 혼란스럽게 합니다.

config의 TMC 드라이버 섹션에 `hold_current` 설정이 지정되지 않았는지 
확인하십시오. (hold_current가 설정되면 접촉 후 캐리지가 레일의 끝 부분에 
대해 눌러지는 동안 모터가 정지하고 해당 위치에서 전류를 줄이면 캐리지가 
움직일 수 있습니다. 이는 성능이 저하 및 튜닝과정 혼란을 초래할 수 있습니다.)

센서리스 귀환 핀을 구성하고 초기 "stallguard" 설정을 구성해야 합니다. 
X축에 대한 tmc2209 예제 구성은 다음과 같습니다:

```
[tmc2209 stepper_x]
diag_pin: ^PA1      # Set to MCU pin connected to TMC DIAG pin
driver_SGTHRS: 255  # 255 is most sensitive value, 0 is least sensitive
...

[stepper_x]
endstop_pin: tmc2209_stepper_x:virtual_endstop
homing_retract_dist: 0
...
```

tmc2130 또는 tmc5160 구성의 예는 다음과 같습니다:
```
[tmc2130 stepper_x]
diag1_pin: ^!PA1 # Pin connected to TMC DIAG1 pin (or use diag0_pin / DIAG0 pin)
driver_SGT: -64  # -64 is most sensitive value, 63 is least sensitive
...

[stepper_x]
endstop_pin: tmc2130_stepper_x:virtual_endstop
homing_retract_dist: 0
...
```

tmc2660 구성의 예는 다음과 같습니다:
```
[tmc2660 stepper_x]
driver_SGT: -64     # -64 is most sensitive value, 63 is least sensitive
...

[stepper_x]
endstop_pin: ^PA1   # Pin connected to TMC SG_TST pin
homing_retract_dist: 0
...
```

위의 예는 센서리스 원점복귀와 관련된 설정만 보여줍니다. 
사용 가능한 모든 옵션은 [구성 참조](Config_Reference.md#tmc-stepper-driver-configuration)를 참조하세요.

### 원점복귀에 가장 높은 민감도 성공적으로 찾기

레일 중앙 근처에 캐리지를 놓습니다. SET_TMC_FIELD 명령을 사용하여 
가장 높은 민감도를 설정합니다. tmc2209의 경우:

```
SET_TMC_FIELD STEPPER=stepper_x FIELD=SGTHRS VALUE=255
```
tmc2130, tmc5160 및 tmc2660의 경우:
```
SET_TMC_FIELD STEPPER=stepper_x FIELD=sgt VALUE=-64
```

그런 다음 `G28 X0` 명령을 실행하고 축이 전혀 움직이지 않는지 확인합니다. 
축이 움직이면 `M112`를 실행하여 프린터를 중지합니다. diag/sg_tst 핀 배선 
또는 구성이 올바르지 않으므로 계속하기 전에 수정해야 합니다.

다음으로 `VALUE` 설정의 감도를 지속적으로 낮추고 `SET_TMC_FIELD` `G28 X0` 명령을 
다시 실행하여 캐리지가 엔드스톱까지 성공적으로 이동하고 정지하게 하는 
가장 높은 감도를 찾습니다. (tmc2209 드라이버의 경우 SGTHRS가 감소하고 
다른 드라이버의 경우 sgt가 증가합니다.) 각 시도를 레일 중앙 근처에서 
시작해야 합니다(필요한 경우 `M84`를 발행한 다음 캐리지를 수동으로 센터). 
가장 높은 감도를 안정적으로 찾을 수 있어야 합니다(감도가 높은 설정은 움직임이 적거나 없음). 
발견된 값을 *maximum_sensitivity*로 기록합니다. 
(캐리지 이동 없이 가능한 최소 감도(SGTHRS=0 또는 sgt=63)를 얻은 경우 
diag/sg_tst 핀 배선 또는 구성에 문제가 있는 것이므로 계속하기 전에 수정해야 합니다.)

maximum_sensitivity를 검색할 때 다른 VALUE 설정으로 이동하는 것이 
편리할 수 있습니다(VALUE 매개변수를 이등분하기 위해). 이 작업을 
수행하는 경우 `M112` 명령을 실행하여 프린터를 중지할 준비를 하십시오. 
매우 낮은 감도로 설정하면 축이 레일 끝에 반복적으로 "쿵"할 수 있기 때문입니다.

각 원점복귀 시도 사이에 몇 초 정도 기다려야 합니다. TMC 드라이버가 stall을 
감지한 후 내부 표시기를 지우고 다른 stall을 감지할 수 있을 때까지 
약간의 시간이 걸릴 수 있습니다.

이러한 튜닝 테스트 중에 `G28 X0` 명령이 축 제한까지 완전히 이동하지 않으면 
일반 이동 명령(예: `G1`)을 실행할 때 주의하십시오. Klipper는 캐리지 위치를 
올바르게 이해하지 못하며 이동 명령은 바람직하지 않고 혼란스러운 결과를 
초래할 수 있습니다.

#### 원터치로 원점복귀에 가장 낮은 민감도 찾기

찾은 *maximum_sensitivity* 값으로 원점 복귀할 때 축은 레일 끝으로 이동하고 
"한 번의 터치"로 정지해야 합니다. 즉, "딸깍" 또는 "쿵" 소리가 없어야 합니다. 
(max_sensitivity에서 두드리는 소리나 딸깍하는 소리가 나면 homing_speed가 
너무 낮거나 드라이버 전류가 너무 낮거나 센서리스 homing이 축에 적합한 
선택이 아닐 수 있습니다.)

다음 단계는 캐리지를 계속해서 레일 중앙 근처의 위치로 이동하고 민감도를 낮추고 
`SET_TMC_FIELD` `G28 X0` 명령을 실행하는 것입니다. 이제 목표는 여전히 
캐리지가 "한 번의 터치"로 성공적으로 원점 복귀할 수 있는 가장 낮은 민감도를 
찾는 것입니다. 즉, 레일 끝부분에 닿았을 때 "쾅"하거나 "딸깍"하지 않습니다. 
발견된 값을 *minimum_sensitivity*로 기록합니다.

#### 민감도 값으로 printer.cfg 업데이트

*maximum_sensitivity* 및 *minimum_sensitivity*를 찾은 후 계산기를 사용하여 
권장 민감도를 *minimum_sensitivity + (maximum_sensitivity - minimum_sensitivity)/3*로 
구합니다. 권장 민감도는 최소값과 최대값 사이의 범위에 있어야 하지만 최소값에 
약간 더 가깝습니다. 최종 값을 가장 가까운 정수 값으로 반올림합니다.

tmc2209의 경우 구성에서 `driver_SGTHRS`로 설정하고 다른 TMC 드라이버의 경우 
구성에서 `driver_SGT`로 설정합니다.

*maximum_sensitivity*와 *minimum_sensitivity* 사이의 범위가 작으면(예: 5 미만) 
불안정한 원점 복귀가 발생할 수 있습니다. 원점복귀 속도가 빠를수록 범위가 
넓어지고 안정적인 작동이 가능합니다.

드라이버 전류, 원점복귀 속도가 변경되거나 프린터 하드웨어가 눈에 띄게 변경되면 
조정 프로세스를 다시 실행해야 합니다.

#### 원점 복귀 시 매크로 사용

센서리스 원점복귀가 완료되면 캐리지가 레일의 끝 부분에 대해 눌려지고 
스테퍼는 캐리지가 멀리 이동할 때까지 프레임에 힘을 가합니다. 축을 홈으로 
이동하고 즉시 캐리지를 레일 끝에서 멀리 이동하는 매크로를 만드는 것이 좋습니다.

센서리스 원점복귀를 시작하기 최소 2초 전에 매크로를 일시 중지하는 것이 
좋습니다(또는 2초 동안 스테퍼에서 움직임이 없는지 확인). 지연 없이 드라이버의 
내부 stall flag가 이전 이동에서 계속 설정될 수 있습니다.

또한 해당 매크로가 원점복귀 전에 드라이버 전류를 설정하고 캐리지가 멀리 
이동한 후에 새 전류를 설정하도록 하는 것이 유용할 수 있습니다. 이것은 또한 
인쇄 중에 hold_current를 설정할 수 있도록 합니다
(hold_current는 센서리스 원점복귀 중에는 권장되지 않음).

예제 매크로는 다음과 같습니다:

```
[gcode_macro SENSORLESS_HOME_X]
gcode:
    {% set HOME_CUR = 0.700 %}
    {% set driver_config = printer.configfile.settings['tmc2209 stepper_x'] %}
    {% set RUN_CUR = driver_config.run_current %}
    {% set HOLD_CUR = driver_config.hold_current %}
    # Set current for sensorless homing
    SET_TMC_CURRENT STEPPER=stepper_x CURRENT={HOME_CUR} HOLDCURRENT={HOME_CUR}
    # Pause to ensure driver stall flag is clear
    G4 P2000
    # Home
    G28 X0
    # Move away
    G90
    G1 X5 F1200
    # Set current during print
    SET_TMC_CURRENT STEPPER=stepper_x CURRENT={RUN_CUR} HOLDCURRENT={HOLD_CUR}
```
결과 매크로는 [homing_override 구성 섹션](Config_Reference.md#homing_override) 또는 [START_PRINT 매크로](Slicers.md#klipper-gcode_macro)에서 호출할 수 있습니다.

원점복귀 중 드라이버 전류가 변경되면 튜닝 프로세스를 다시 실행해야 합니다.

### CoreXY에서 센서리스 원점복귀를 위한 팁

CoreXY 프린터의 X 및 Y 캐리지에서 센서리스 원점복귀를 사용할 수 있습니다. 
Klipper는 `[stepper_x]` 스테퍼를 사용하여 X 캐리지를 원점복귀할 때 stall을 감지하고 
`[stepper_y]` 스테퍼를 사용하여 Y 캐리지를 원점복귀할 때 stall을 감지합니다.

위에서 설명한 조정 가이드를 사용하여 각 캐리지에 적절한 "stall 민감도"를 찾으십시오. 
단, 다음 제한 사항에 유의하십시오:

1. CoreXY에서 센서리스 원점복귀를 사용할 때 원점복귀 중 두 스테퍼에 대해 
   `hold_current`가 적용되지 않는지 확인하십시오.
2. 조정하는 동안 각 원점복귀 시도 전에 X 및 Y 캐리지가 레일 중앙 근처에 
   있는지 확인하십시오.
3. 조정이 완료된 후 X와 Y를 모두 원점복귀시킬 때 매크로를 사용하여 한 축이 먼저 
   원점복귀되도록 한 다음 해당 캐리지를 축 제한에서 멀리 이동하고 최소 2초 동안 
   일시 중지한 다음 다른 캐리지의 원점복귀를 시작합니다. 축에서 멀어지면 
   다른 축이 축 제한에 대해 눌러지는 동안 한 축의 원점복귀를 방지합니다
   (스톨 감지가 왜곡될 수 있음). 다시 원점복귀하기 전에 드라이버의 stall flag가 
   지워지도록 하려면 일시 중지가 필요합니다.

## 드라이버 설정 쿼리 및 진단

`[DUMP_TMC 명령](G-Codes.md#tmc-stepper-drivers)은 드라이버를 구성하고 진단할 때 
유용한 도구입니다. Klipper가 구성한 모든 필드와 드라이버에서 쿼리할 수 있는 
모든 필드를 보고합니다.

보고된 모든 필드는 각 드라이버에 대한 Trinamic 데이터시트에 정의되어 있습니다. 
이 데이터시트는 [Trinamic 웹사이트](https://www.trinamic.com/)에서 찾을 수 있습니다. 
드라이버가 DUMP_TMC의 결과를 해석할 수 있도록 Trinamic 데이터시트를 
구하고 검토하십시오.

## driver_XXX 설정 구성

Klipper는 `driver_XXX` 설정을 사용하여 많은 하위 수준 드라이버 필드 구성을 지원합니다. 
[TMC 드라이버 구성 참조](Config_Reference.md#tmc-stepper-driver-configuration)에는 각 드라이버 유형에 사용할 수 있는 전체 필드 목록이 있습니다.

또한 [SET_TMC_FIELD 명령](G-Codes.md#tmc-stepper-drivers)을 사용하여 런타임 시 거의 모든 필드를 수정할 수 있습니다.

이러한 각 필드는 각 드라이버에 대한 Trinamic 데이터시트에 정의되어 있습니다. 
이 데이터시트는 [Trinamic 웹사이트](https://www.trinamic.com/)에서 찾을 수 있습니다.

Trinamic 데이터시트는 때때로 높은 수준의 설정(예: "hysteresis end")과 낮은 수준의 
필드 값(예: "HEND")을 혼동할 수 있는 문구를 사용합니다. Klipper에서 
`driver_XXX`와 SET_TMC_FIELD는 항상 실제로 드라이버에 기록되는 
하위 수준 필드 값을 설정합니다. 따라서 예를 들어 Trinamic 데이터시트에 
"hysteresis end" 0을 얻기 위해 값 3을 HEND 필드에 기록해야 한다고 
명시되어 있으면 `driver_HEND=3`을 설정하여 상위 수준 값 0을 얻습니다.

## 일반적인 질문

### pressure advance 기능이 있는 압출기에서  stealthchop 모드를 사용할 수 있습니까?

많은 사람들이 Klipper의 pressure advance으로 "stealthchop" 모드를 성공적으로 사용합니다. 
Klipper는 즉각적인 속도 변화를 도입하지 않는 [smooth pressure advance](Kinematics.md#pressure-advance)을 구현합니다.

그러나 "stealthchop" 모드는 더 낮은 모터 토크를 생성하거나 더 높은 모터 열을 
생성할 수 있습니다. 특정 프린터에 적합한 모드일 수도 있고 아닐 수도 있습니다.

### "Unable to read tmc uart 'stepper_x' register IFCNT" 오류가 계속 발생합니까?

이것은 Klipper가 tmc2208 또는 tmc2209 드라이버와 통신할 수 없을 때 발생합니다.

스테퍼 모터 드라이버는 일반적으로 마이크로 컨트롤러와 통신하기 전에 모터 전원이 
필요하므로 모터 전원이 활성화되어 있는지 확인하십시오.

이 오류가 Klipper를 처음 깜박인 후 발생하면 스테퍼 드라이버가 이전에 Klipper와 
호환되지 않는 상태로 프로그래밍되었을 수 있습니다. 상태를 재설정하려면 
몇 초 동안 프린터에서 모든 전원을 제거하십시오(USB와 전원 플러그를 모두 물리적으로 분리).

그렇지 않으면 이 오류는 일반적으로 잘못된 UART 핀 배선 또는 UART 핀 설정의 
잘못된 Klipper 구성의 결과입니다.

### "Unable to write tmc spi 'stepper_x' register ..." 오류가 계속 발생합니까?

이것은 Klipper가 tmc2130 또는 tmc5160 드라이버와 통신할 수 없을 때 발생합니다.

스테퍼 모터 드라이버는 일반적으로 마이크로 컨트롤러와 통신하기 전에 
모터 전원이 필요하므로 모터 전원이 활성화되어 있는지 확인하십시오.

그렇지 않으면 이 오류는 일반적으로 잘못된 SPI 배선, SPI 설정의 잘못된 Klipper 구성 
또는 SPI 버스의 장치 구성이 불완전한 결과입니다.

드라이버가 여러 장치가 있는 공유 SPI 버스에 있는 경우 Klipper에서 해당 공유 SPI 버스의 
모든 장치를 완전히 구성해야 합니다. 공유 SPI 버스의 장치가 구성되지 않은 경우 
의도하지 않은 명령에 잘못 응답하고 의도한 장치에 대한 통신이 손상될 수 있습니다. 
Klipper에서 구성할 수 없는 공유 SPI 버스에 장치가 있는 경우 [static_digital_output config 섹션](Config_Reference.md#static_digital_output)을
사용하여 사용하지 않는 장치의 CS 핀을 높음으로 설정합니다. SPI 버스를 사용하기 위해
보드의 회로도는 SPI 버스 및 관련 핀에 있는 장치를 찾는 데 유용한 참조가 되는 경우가 많습니다.

### "TMC 보고서 오류: ..." 오류가 발생한 이유는 무엇입니까?

이 유형의 오류는 TMC 드라이버가 문제를 감지하고 자체적으로 비활성화되었음을 
나타냅니다. 즉, 드라이버가 위치 유지를 중지하고 이동 명령을 무시했습니다. 
Klipper는 활성 드라이버가 자체적으로 비활성화되었음을 감지하면 프린터를 
"shutdown" 상태로 전환합니다.

몇 가지 일반적인 오류 및 진단 팁:

**TMC reports error: ... ot=1(OvertempError!)"**: 이것은 모터 드라이버가 
너무 뜨거워져서 스스로 비활성화되었음을 나타냅니다. 일반적인 솔루션은 
스테퍼 모터 전류를 낮추고, 스테퍼 모터 드라이버의 냉각을 높이거나, 
스테퍼 모터의 냉각을 높이는 것입니다.

**TMC reports error: ... ShortToGND** OR **LowSideShort**: 이것은 드라이버를 
통과하는 매우 높은 전류를 감지했기 때문에 드라이버가 스스로 비활성화되었음을 
나타냅니다. 이것은 스테퍼 모터 또는 스테퍼 모터 자체 내부의 느슨하거나 
단락된 와이어를 나타낼 수 있습니다.

이 오류는 stealthchop 모드를 사용하고 TMC 드라이버가 모터의 기계적 부하를 
정확하게 예측할 수 없는 경우에도 발생할 수 있습니다. (드라이버가 잘못된 예측을 
하면 모터를 통해 너무 많은 전류를 보내고 자체 과전류 감지를 트리거할 수 있습니다.) 
이를 테스트하려면 stealthchop 모드를 비활성화하고 오류가 계속 발생하는지 확인하십시오.

**TMC reports error: ... reset=1(Reset)** OR **CS_ACTUAL=0(Reset?)**
OR **SE=0(Reset?)**: 이것은 드라이버가 인쇄 중간에 재설정되었음을
나타냅니다. 이는 전압 또는 배선 문제로 인한 것일 수 있습니다.

**TMC reports error: ... uv_cp=1(Undervoltage!)**: 이는 드라이버가 
저전압 이벤트를 감지하고 자체적으로 비활성화되었음을 나타냅니다. 
이는 배선 또는 전원 공급 문제로 인한 것일 수 있습니다.

드라이버와의 통신을 방해하는 SPI 오류로 인해 **TMC가 오류 보고** 종료가 발생할 
수도 있습니다(tmc2130, tmc5160 또는 tmc2660에서). 이 경우 보고된 드라이버 
상태가 `00000000` 또는 `ffffffff`로 표시되는 것이 일반적입니다. 
예: `TMC 보고 오류: DRV_STATUS: ffffffff ...' 
또는 `TMC 보고 오류: READRSP@RDSEL2: 00000000 ... `. 
이러한 오류는 SPI 배선 문제로 인한 것일 수도 있고 자체 재설정 또는 
TMC 드라이버의 오류로 인한 것일 수도 있습니다.

### Spreadcycle/coolstep 등을 어떻게 조정합니까? 내 드라이버의 모드?

[Trinamic 웹사이트](https://www.trinamic.com/)에는 드라이버 구성에 대한 가이드가 있습니다. 
이 가이드는 종종 기술적이고 낮은 수준이며 특수 하드웨어가 필요할 수 있습니다. 
그럼에도 불구하고 그들은 최고의 정보 소스입니다.
