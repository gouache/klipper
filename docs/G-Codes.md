# G-Codes

이 문서는 Klipper가 지원하는 명령에 대해 설명합니다. OctoPrint Terminal 탭에 입력할 수 있는 명령입니다.

## G-Code 명령어

Klipper는 다음과 같은 표준 G-Code 명령을 지원합니다:
- 이동 (G0 또는 G1): `G1 [X<위치>] [Y<위치>] [Z<위치>] [E<위치>] [F<속도>]`
- 지연(멈춤): `G4 P<밀리세컨드>`
- 원점으로 복귀: `G28 [X] [Y] [Z]`
- 모터 끄기: `M18` 또는 `M84`
- 현재 동작이 완료될 때까지 대기: `M400`
- 익스트루더 사출에 절대/상대 거리 사용: `M82`, `M83`
- 절대/상대 좌표 사용: `G90`, `G91`
- 위치 설정: `G92 [X<위치>] [Y<위치>] [Z<위치>] [E<위치>]`
- Set speed factor override percentage: `M220 S<백분율>`
- Set extrude factor override percentage: `M221 S<백분율>`
- 가속도 설정: `M204 S<값>` 또는 `M204 P<값> T<값>`
 - 참고: S를 지정하지 않고 P와 T를 모두 지정하면 가속도는 P와 T의 최소값으로 설정됩니다. P 또는 T 중 하나만 지정하면 명령이 적용되지 않습니다.
- 익스트루더(베드) 온도 가져오기: `M105`
- 익스트루더 온도 설정: `M104 [T<인덱스>] [S<온도>]`
- 익스트루더 온도 설정될때 까지 기다리기: `M109 [T<인덱스>] S<온도>`
  - Note: 참고: M109는 항상 온도가 요청된 값으로 안정될 때까지 기다립니다.
- 베드 온도 설정: `M140 [S<온도>]`
- 베드 온도 설정될때 까지 기다리기: `M190 S<온도>`
  - 참고: M190는 항상 온도가 요청된 값으로 안정될 때까지 기다립니다.
- 팬 속도 설정: `M106 S<값>`
- 팬 끄기: `M107`
- 비상 정지: `M112`
- 현재 위치 가져오기: `M114`
- 펌웨어 버전 가져오기: `M115`

위 명령어에 대한 자세한 내용은 
[RepRap G-Code documentation](http://reprap.org/wiki/G-code) 를 참조하십시오.

Klipper 의 목표는 표준 구성에서 일반적인 타사 소프트웨어 
(예: OctoPrint, Printrun, Slic3r, Cura 등)에서 생성된 G-Code 명령을 지원하는 것입니다. 
가능한 모든 G-Code 명령을 지원하는 것이 목표는 아닙니다. 대신 Klipper 는 사람이 읽을 수 있는 
["extended G-Code commands"](#extended-g-code-commands)을 선호합니다.

덜 일반적인 G-Code 명령이 필요한 경우 사용자 지정 
[gcode_macro config section](Config_Reference.md#gcode_macro)으로 이를 구현할 수 
있습니다. 예를 들어, `G12`, `G29`, `G30`, `G31`, `M42`, `M80`, `M81`, `T1` 등을 
구현하는 데 사용할 수 있습니다.

### G-Code SD 카드 명령

Klipper는 [virtual_sdcard config section](Config_Reference.md#virtual_sdcard) 이 
활성화된 경우 다음 표준 G-Code 명령도 지원합니다:
- SD 카드 목록 보기: `M20`
- SD 카드 초기화: `M21`
- SD 파일 선택: `M23 <filename>`
- SD 프린트 시작/재시작: `M24`
- SD 프린트 잠시멈춤: `M25`
- SD 위치 설정: `M26 S<offset>`
- SD 프린트 상태 리포트: `M27`

또한 "virtual_sdcard" 구성 섹션이 활성화된 경우 다음 확장 명령을 사용할 수 있습니다.
- SD 파일 로드 및 프린트: `SDCARD_PRINT_FILE FILENAME=<filename>`
- 파일 언로드 및 SD 상태 지우기: `SDCARD_RESET_FILE`

### arcs

[gcode_arcs config section](Config_Reference.md#gcode_arcs)이 활성화된 경우 
다음 표준 G-Code 명령을 사용할 수 있습니다.
- Controlled Arc Move (G2 or G3): `G2 [X<pos>] [Y<pos>] [Z<pos>]
  [E<pos>] [F<speed>] I<value> J<value>`

### 펌웨어 리트랙션

[firmware_retraction config section](Config_Reference.md#firmware_retraction) 이 
활성화된 경우 다음 표준 G-Code 명령을 사용할 수 있습니다.
- Retract: `G10`
- Unretract: `G11`

### 디스플레이 명령

[display config section](Config_Reference.md#display)이 활성화된 경우 다음 표준 
G-코드 명령을 사용할 수 있습니다.
- Display Message: `M117 <message>`
- Set build percentage: `M73 P<percent>`

### 기타 사용 가능한 G 코드 명령

다음 표준 G-Code 명령을 현재 사용할 수 있지만 사용하지 않는 것이 좋습니다.
- Get Endstop Status: `M119` (대신 QUERY_ENDSTOPS 사용하세요)

## 확장된 G 코드 명령

Klipper는 일반 구성 및 상태에 대해 "확장된" G-Code 명령을 사용합니다. 이러한 확장 
명령은 모두 유사한 형식을 따릅니다. 명령 이름으로 시작하고 하나 이상의 매개변수가 뒤따를 수 있습니다.
예제: `SET_SERVO SERVO=myservo ANGLE=5.3`. 
이 문서에서 명령과 매개변수는 대문자로 표시되지만 대소문자를 구분하지 않습니다. 
(따라서 "SET_SERVO"와 "set_servo"는 모두 동일한 명령을 실행합니다.)

다음 표준 명령이 지원됩니다:
- `QUERY_ENDSTOPS`: 엔드스톱을 조사하고 "triggered" 되었는지 또는 "open" 상태인지 보고합니다.
  이 명령은 일반적으로 엔드스톱이 올바르게 작동하는지 확인하는 데 사용됩니다.
- `QUERY_ADC [NAME=<config_name>] [PULLUP=<value>]`: 구성된 아날로그 핀에 대해 수신된 
  마지막 아날로그 값을 보고합니다. NAME 이 제공되지 않으면 사용 가능한 adc 이름 목록이 보고됩니다. 
  PULLUP 이 제공되면 (Ohms 단위의 값으로) 해당 풀업이 제공된 등가 저항과 함께 원시 아날로그 값이 
  보고됩니다.
- `GET_POSITION`: 툴헤드의 현재 위치에 대한 정보를 반환합니다.
- `SET_GCODE_OFFSET [X=<pos>|X_ADJUST=<adjust>] 
  [Y=<pos>|Y_ADJUST=<adjust>] [Z=<pos>|Z_ADJUST=<adjust>] 
  [MOVE=1 [MOVE_SPEED=<speed>]]`: 향후 G-Code 명령에 적용할 위치 오프셋을 설정합니다.
  것은 일반적으로 Z 베드 오프셋을 가상으로 변경하거나 압출기를 전환할 때 노즐 XY 오프셋을 
  설정하는 데 사용됩니다. 예를 들어 "SET_GCODE_OFFSET Z=0.2"가 전송되면 향후 G-Code 
  이동은 Z 높이에 0.2mm가 추가됩니다.X_ADJUST 스타일 매개변수를 사용하는 경우 조정이 기존 
  오프셋에 추가됩니다. (예: "SET_GCODE_OFFSET Z = -0.2" 다음에 
  "SET_GCODE_OFFSET Z_ADJUST = 0.3"을 입력하면 총 Z 오프셋이 0.1이 됩니다.) 
  "MOVE=1"이 지정되면 지정된 오프셋을 적용하기 위해 툴 헤드 이동이 실행됩니다. 
  (그렇지 않으면 오프셋은 주어진 축을 지정하는 다음 절대 G 코드 이동에 적용됩니다.)
  "MOVE_SPEED"가 지정되면 지정된 속도(mm/s)로 툴 헤드 이동이 수행됩니다; 그렇지 않으면 
  툴 헤드 이동은 마지막으로 지정된 G 코드 속도를 사용합니다.
- `SAVE_GCODE_STATE [NAME=<state_name>]`: 현재 g-code 좌표 구문 분석 상태를 저장합니다. 
  g-code 상태를 저장하고 복원하는 것은 스크립트와 매크로에서 유용합니다. 
  이 명령은 현재 g-code 절대 좌표 모드(G90/G91), 절대 압출 모드(M82/M83), 원점(G92), 
  오프셋(SET_GCODE_OFFSET), 속도 오버라이드(M220), 압출기 오버라이드(M221), 현재 XYZ 위치 
  및 상대 압출기 "E" 위치 그리고 이동 속도를 저장합니다,   
  NAME 이 제공되면 저장된 상태의 이름을 주어진 문자열로 지정할 수 있습니다.
  NAME 이 제공되지 않으면 기본값은 "default" 입니다.
- `RESTORE_GCODE_STATE [NAME=<state_name>]
  [MOVE=1 [MOVE_SPEED=<speed>]]`: SAVE_GCODE_STATE 를 통해 이전에 저장한 상태를 복원합니다.
  "MOVE=1"이 지정되면 이전 XYZ 위치로 다시 이동하기 위해 툴 헤드 이동이 실행됩니다.
  "MOVE_SPEED"가 지정되면 지정된 속도(mm/s)로 툴 헤드 이동이 수행됩니다;
  그렇지 않으면 툴헤드 이동은 복원된 g-코드 속도를 사용합니다.
- `PID_CALIBRATE HEATER=<config_name> TARGET=<temperature>
  [WRITE_FILE=1]`: PID 캘리브레이션 테스트를 수행합니다.
  지정된 히터는 지정된 목표 온도에 도달할 때까지 활성화되며, 그런 다음 히터가 여러 사이클 동안
  꺼졌다 켜집니다. WRITE_FILE 매개변수가 활성화된 경우, /tmp/heattest.txt 파일이 테스트 중에 
  가져온 모든 온도 샘플의 로그와 함께 생성됩니다.
- `TURN_OFF_HEATERS`: 모든 히터를 끕니다.
- `TEMPERATURE_WAIT SENSOR=<config_name> [MINIMUM=<target>] [MAXIMUM=<target>]`:
  주어진 온도 센서가 제공된 최소값 이상 및/또는 제공된 최대값 이하일 때까지 기다립니다.
- `SET_VELOCITY_LIMIT [VELOCITY=<value>] [ACCEL=<value>]
  [ACCEL_TO_DECEL=<value>] [SQUARE_CORNER_VELOCITY=<value>]`: 프린터의 속도 제한을 수정합니다.
- `SET_HEATER_TEMPERATURE HEATER=<heater_name> [TARGET=<target_temperature>]`:
  히터의 목표 온도를 설정합니다. 목표 온도가 제공되지 않으면 목표는 0입니다.
- `ACTIVATE_EXTRUDER EXTRUDER=<config_name>`: 여러 압출기가 있는 프린터에서 
  이 명령은 활성 압출기를 변경하는 데 사용됩니다.
- `SET_PRESSURE_ADVANCE [EXTRUDER=<config_name>] [ADVANCE=<pressure_advance>]
  [SMOOTH_TIME=<pressure_advance_smooth_time>]`: 압력 보정 매개변수를 설정합니다.
  EXTRUDER를 지정하지 않으면 기본적으로 현재 사용중인 익스트루더가 설정됩니다.
- `SET_EXTRUDER_STEP_DISTANCE [EXTRUDER=<config_name>]
  [DISTANCE=<distance>]`: 제공된 익스트루더의  "step distance "에 대한 새 값을 설정합니다. 
  "step distance" 는 `rotation_distance/(full_steps_per_rotation*microsteps)` 입니다.
  Klipper 재설정 시 값이 유지되지 않습니다. 주의해서 사용하십시오. 
  작은 변화로도 압출기와 핫 엔드 사이에 과도한 압력이 발생할 수 있습니다. 
  사용하기 전에 필라멘트로 적절한 보정 단계를 수행하십시오. 
  'DISTANCE' 값이 포함되지 않은 경우 명령은 현재 단계 DISTANCE 를 반환합니다.
- `SET_STEPPER_ENABLE STEPPER=<config_name> ENABLE=[0|1]`: 지정된 스테퍼만 활성화 
  또는 비활성화합니다. 이것은 진단 및 디버깅 도구이며 주의해서 사용해야 합니다. 축 모터를 비활성화해도 
  원점 복귀 정보는 재설정되지 않습니다. 비활성화된 스테퍼를 수동으로 이동하면 기계가 안전 한계를 벗어나 
  모터를 작동할 수 있습니다. 이로 인해 축 구성 요소, 핫 엔드 및 인쇄 표면이 손상될 수 있습니다.
- `STEPPER_BUZZ STEPPER=<config_name>`: 주어진 스테퍼를 앞으로 1mm 이동한 다음 뒤로 1mm 
  이동하고 10회 반복합니다. 이것은 스테퍼 연결을 확인하는 데 도움이 되는 진단 도구입니다.
- `MANUAL_PROBE [SPEED=<speed>]`: 주어진 위치에서 노즐 높이를 측정하는 데 유용한 도우미 
  스크립트를 실행합니다. SPEED가 지정되면 TESTZ 명령의 속도를 설정합니다(기본값은 5mm/s).
  수동 프로브 중에 다음과 같은 추가 명령을 사용할 수 있습니다:
  - `ACCEPT`: 이 명령은 현재 Z 위치를 받아들이고 수동 프로빙을 종료합니다.
  - `ABORT`: 이 명령은 수동 프로빙을 종료합니다.
  - `TESTZ Z=<value>`: 이 명령은 "value" 에 지정된 양만큼 노즐을 위 또는 아래로 이동합니다. 
    예를 들어 `TESTZ Z=-.1`은 노즐을 0.1mm 아래로 이동하고 `TESTZ Z=.1`은 노즐을 0.1mm 
    위로 이동합니다. 값은 또한 '+', '-', '++' 또는 '--'로 노즐을 이전 시도에 비해 위 또는 
    아래로 움직일 수 있습니다.
- `Z_ENDSTOP_CALIBRATE [SPEED=<speed>]`: Z position_endstop 구성 설정을 보정하는 데
   유용한 도우미 스크립트를 실행합니다. 툴이 활성화되어 있는 동안 사용할 수 있는 매개변수 및 추가 
   명령에 대한 자세한 내용은 MANUAL_PROBE 명령을 참조하십시오.
- `Z_OFFSET_APPLY_ENDSTOP`: 현재 Z Gcode 오프셋(일명 babystepping)을 가져 와서 
  stepper_z endstop_position에서 뺍니다.이것은 자주 사용하는 베이비 스테핑 값을 가져 와서 
  "영구화"하는 역할을 합니다. 적용하려면 'SAVE_CONFIG'가 필요합니다.
- `TUNING_TOWER COMMAND=<command> PARAMETER=<name> 
  START=<value> FACTOR=<value> [BAND=<value>]`: 인쇄하는 동안 각 Z 높이에서 매개변수를 
  조정하기 위한 도구입니다. 이것은 `value = start + factor * z_height` 공식을 사용하여 값에 
  지정된 PARAMETER로 지정된 COMMAND를 실행합니다. BAND가 제공되면 z 높이의 BAND 밀리미터마다 
  조정이 이루어집니다. 이 경우 사용되는 공식은 
  `value = start + factor * ((floor(z_height / band) + .5) * band)`입니다.
- `SET_DISPLAY_GROUP [DISPLAY=<display>] GROUP=<group>`: LCD 디스플레이의 활성 
  디스플레이 그룹을 설정합니다. 이를 통해 구성에서 여러 디스플레이 데이터 그룹을 정의할 수 있습니다, 
  예를 들어 `[display_data <group> <elementname>]`을 선택하고 이 확장된 gcode 명령을 
  사용하여 둘 사이를 전환합니다. DISPLAY가 지정되지 않은 경우 기본값은 "display" 
  (기본 디스플레이)입니다.
- `SET_IDLE_TIMEOUT [TIMEOUT=<timeout>]`:  사용자가 유휴 시간 초과(초)를 설정할 수 있습니다.
- `RESTART`: 호스트 소프트웨어가 구성을 다시 로드하고 내부 재설정을 수행합니다. 
  이 명령은 마이크로 컨트롤러에서 오류 상태를 지우지 않습니다. (FIRMWARE_RESTART 참조) 
  새 소프트웨어를 로드하지도 않습니다([FAQ](FAQ.md#how-do-i-upgrade-to-the-latest-software 참조)).
- `FIRMWARE_RESTART`: 이는 RESTART 명령과 유사하지만 마이크로 컨트롤러의 모든 오류 상태도 지웁니다
- `SAVE_CONFIG`: 이 명령은 기본 프린터 config 파일을 덮어쓰고 호스트 소프트웨어를 다시 시작합니다. 
  이 명령은 캘리브레이션 테스트 결과를 저장하기 위해 다른 캘리브레이션 명령과 함께 사용됩니다.
- `STATUS`: Klipper 호스트 소프트웨어 상태를 보고합니다.
- `HELP`: 사용 가능한 확장 G 코드 명령 목록을 보고합니다.

### 매크로 명령

다음 명령은 [gcode_macro config section](Config_Reference.md#gcode_macro)이 
활성화된 경우 사용할 수 있습니다([command templates guide](Command_Templates.md) 참조):
- `SET_GCODE_VARIABLE MACRO=<macro_name> VARIABLE=<name> VALUE=<value>`: 
  이 명령을 사용하면 런타임에 gcode_macro 변수의 값을 변경할 수 있습니다. 
  제공된 VALUE는 Python 리터럴로 구문 분석됩니다.

### 사용자 정의 핀 명령

다음 명령은 [output_pin config section](Config_Reference.md#output_pin)이 
활성화된 경우 사용할 수 있습니다.
- `SET_PIN PIN=config_name VALUE=<value> CYCLE_TIME=<cycle_time>`

참고: 하드웨어 PWM 은 현재 CYCLE_TIME 매개변수를 지원하지 않으며 config 에 정의된 주기 시간을 사용합니다.

### 수동으로 제어되는 팬 명령

e[fan_generic config section](Config_Reference.md#fan_generic) 이 
활성화된 경우 다음 명령을 사용할 수 있습니다
- `SET_FAN_SPEED FAN=config_name SPEED=<speed>` 
  이 명령은 팬의 속도를 설정합니다. <speed>는 0.0에서 1.0 사이여야 합니다.

### Neopixel 및 Dotstar 명령

다음 명령은 [neopixel config section](Config_Reference.md#neopixel) 또는 
[dotstar config section](Config_Reference.md#dotstar)이 활성화된 경우 사용할 수 있습니다:
- `SET_LED LED=<config_name> RED=<value> GREEN=<value> 
  BLUE=<value> WHITE=<value> [INDEX=<index>] [TRANSMIT=0] [SYNC=1]`: 
  LED 출력을 설정합니다.
  각 색상 `<value>`은 0.0에서 1.0 사이여야 합니다. WHITE 옵션은 RGBW LED에서만 유효합니다. 
  여러 LED 칩이 데이지 체인 방식으로 연결되어 있는 경우 INDEX를 지정하여 주어진 칩의 
  색상만 변경할 수 있습니다 (첫 번째 칩은 1, 두 번째 칩은 2 등). 
  INDEX가 제공되지 않으면 데이지 체인의 모든 LED 가 제공된 색상으로 설정됩니다. 
  TRANSMIT=0 이 지정되면 색상 변경은 TRANSMIT=0 을 지정하지 않는 다음 SET_LED 명령에서만 
  이루어집니다; 이것은 데이지 체인에서 여러 업데이트를 일괄 처리하기 위해 INDEX 매개변수와 함께 
  유용할 수 있습니다. 기본적으로 SET_LED 명령은 변경 사항을 진행 중인 다른 gcode 명령과 
  동기화합니다. 이것은 유휴 시간 초과를 재설정하므로 프린터가 인쇄하지 않는 동안 LED가 설정되는 
  경우 바람직하지 않은 동작으로 이어질 수 있습니다. 신중한 타이밍이 필요하지 않은 경우 선택적 
  SYNC=0 매개변수를 지정하여 변경 사항을 즉시 적용하고 유휴 시간 초과를 재설정하지 않을 수 있습니다.

### 서보 명령어

 [servo config section](Config_Reference.md#servo) 이 활성화된 경우 다음 명령을 
 사용할 수 있습니다:
- `SET_SERVO SERVO=config_name [ANGLE=<degrees> | WIDTH=<seconds>]`: 
  서보 위치를 주어진 각도(도 단위) 또는 펄스 폭(초 단위)으로 설정합니다. 서보 출력을 
  비활성화하려면 `WIDTH=0`을 사용하십시오.

### 수동 스테퍼 명령

다음 명령은 [manual_stepper config section](Config_Reference.md#manual_stepper)이 
활성화된 경우 사용할 수 있습니다.
- `MANUAL_STEPPER STEPPER=config_name [ENABLE=[0|1]] [SET_POSITION=<pos>] 
  [SPEED=<speed>] [ACCEL=<accel>] [MOVE=<pos> [STOP_ON_ENDSTOP=[1|2|-1|-2]] 
  [SYNC=0]]`: 이 명령은 스테퍼의 상태를 변경합니다. ENABLE 매개변수를 사용하여 스테퍼를 
  활성화/비활성화합니다. SET_POSITION 매개변수를 사용하여 스테퍼가 지정된 위치에 있다고 
  생각하도록 합니다. MOVE 매개변수를 사용하여 지정된 위치로 이동을 요청합니다. 
  SPEED 및/또는 ACCEL이 지정되면 구성 파일에 지정된 기본값 대신 지정된 값이 사용됩니다.
  0의 ACCEL이 지정되면 가속이 수행되지 않습니다. STOP_ON_ENDSTOP=1 이 지정되면 endstop 
  보고서가 트리거되면 이동이 일찍 종료됩니다 (엔드스톱이 트리거되지 않더라도 오류 없이 이동을 
  완료하려면 STOP_ON_ENDSTOP=2를 사용하고, 엔드스톱이 트리거되지 않았다고 보고할 때 중지하려면 
  -1 또는 -2를 사용).
  일반적으로 향후 G-Code 명령은 스테퍼 이동이 완료된 후 실행되도록 예약되지만 수동 스테퍼 이동이 
  SYNC=0을 사용하는 경우 향후 G-Code 이동 명령은 스테퍼 이동과 병렬로 실행될 수 있습니다.

### 익스트루더 스테퍼 명령

다음 명령은 [extruder_stepper config section](Config_Reference.md#extruder_stepper) 
이 활성화된 경우 사용할 수 있습니다:
- `SYNC_STEPPER_TO_EXTRUDER STEPPER=<extruder_stepper config_name> 
  [EXTRUDER=<extruder config_name>]`: 이 명령은 지정된 STEPPER가 "extruder_stepper" 
  구성 섹션에 정의된 압출기를 재정의하여 지정된 EXTRUDER와 동기화되도록 합니다.

### 프로브

다음 명령은 [probe config section](Config_Reference.md#probe)이 
활성화된 경우 사용할 수 있습니다([probe calibrate guide](Probe_Calibrate.md) 참조):
- `PROBE [PROBE_SPEED=<mm/s>] [LIFT_SPEED=<mm/s>] [SAMPLES=<count>] 
  [SAMPLE_RETRACT_DIST=<mm>] [SAMPLES_TOLERANCE=<mm>] 
  [SAMPLES_TOLERANCE_RETRIES=<count>] [SAMPLES_RESULT=median|average]`: 
  프로브가 트리거될 때까지 노즐을 아래로 이동합니다. 선택적 매개변수가 제공되면 
  [probe config section](Config_Reference.md#probe) 에서 해당 설정을 재정의합니다.
- `QUERY_PROBE`: 프로브의 현재 상태를 보고합니다("triggered" 또는 "open").
- `PROBE_ACCURACY [PROBE_SPEED=<mm/s>] [SAMPLES=<count>] 
  [SAMPLE_RETRACT_DIST=<mm>]`: 여러 프로브 샘플의 최대, 최소, 평균, 중앙값 및 표준 
  편차를 계산합니다. 기본적으로 10개의 샘플이 사용됩니다. 그렇지 않으면 선택적 매개변수는 
  기본적으로 프로브 구성 섹션의 해당 설정으로 설정됩니다.
- `PROBE_CALIBRATE [SPEED=<speed>] [<probe_parameter>=<value>]`: 
  프로브의 z_offset 을 보정하는 데 유용한 도우미 스크립트를 실행합니다.
  선택적 프로브 매개변수에 대한 자세한 내용은 PROBE 명령을 참조하십시오. 
  SPEED 매개변수 및 도구가 활성화되어 있는 동안 사용할 수 있는 추가 명령에 대한 자세한 내용은 MANUAL_PROBE 명령을 참조하십시오.
  PROBE_CALIBRATE 명령은 속도 변수를 사용하여 Z뿐만 아니라 XY 방향으로 이동합니다.
- `Z_OFFSET_APPLY_PROBE`: Take the current Z Gcode offset (aka, babystepping), and subtract if from the probe's z_offset.
  이것은 자주 사용하는 베이비 스테핑 값을 가져 와서 "영구화"하는 역할을 합니다.
  적용하려면 'SAVE_CONFIG'가 필요합니다.

### BLTouch

The following command is available when a [bltouch config section](Config_Reference.md#bltouch) is enabled (also see the [BL-Touch guide](BLTouch.md)):
- `BLTOUCH_DEBUG COMMAND=<command>`: This sends a command to the BLTouch. It may be useful for debugging. Available commands are: `pin_down`, `touch_mode`, `pin_up`, `self_test`, `reset`, (*1): `set_5V_output_mode`, `set_OD_output_mode`, `output_mode_store`

  *** Note that the commands marked by (*1) are solely supported by a BL-Touch V3.0 or V3.1(+)

- `BLTOUCH_STORE MODE=<output_mode>`: This stores an output mode in the EEPROM of a BLTouch V3.1 Available output_modes are: `5V`, `OD`

### 델타 캘리브레이션

The following commands are available when the [delta_calibrate config section](Config_Reference.md#linear-delta-kinematics) is enabled (also see the [delta calibrate guide](Delta_Calibrate.md)):
- `DELTA_CALIBRATE [METHOD=manual] [<probe_parameter>=<value>]`: This command will probe seven points on the bed and recommend updated endstop positions, tower angles, and radius. 
  See the PROBE command for details on the optional probe parameters. 
  If METHOD=manual is specified then the manual probing tool is activated - see the MANUAL_PROBE command above for details on the additional commands available while this tool is active.
- `DELTA_ANALYZE`: This command is used during enhanced delta calibration. See [Delta Calibrate](Delta_Calibrate.md) for details.

### BED Tilt

The following commands are available when the [bed_tilt config section](Config_Reference.md#bed_tilt) is enabled:
- `BED_TILT_CALIBRATE [METHOD=manual] [<probe_parameter>=<value>]`: This command will probe the points specified in the config and then recommend updated x and y tilt adjustments. 
  See the PROBE command for details on the optional probe parameters. 
  If METHOD=manual is specified then the manual probing tool is activated - see the MANUAL_PROBE command above for details on the additional commands available while this tool is active.

### Mesh Bed 레벨링

The following commands are available when the [bed_mesh config section](Config_Reference.md#bed_mesh) is enabled (also see the [bed mesh guide](Bed_Mesh.md)):
- `BED_MESH_CALIBRATE [METHOD=manual] [<probe_parameter>=<value>] [<mesh_parameter>=<value>]`: This command probes the bed using generated points specified by the parameters in the config. 
  After probing, a mesh is generated and z-movement is adjusted according to the mesh. See the PROBE command for details on the optional probe parameters. 
  If METHOD=manual is specified then the manual probing tool is activated - see the MANUAL_PROBE command above for details on the additional commands available while this tool is active.
- `BED_MESH_OUTPUT PGP=[<0:1>]`: This command outputs the current probed z values and current mesh values to the terminal.  
  If PGP=1 is specified the x,y coordinates generated by bed_mesh, along with their associated indices, will be output to the terminal.
- `BED_MESH_MAP`: Like to BED_MESH_OUTPUT, this command prints the current state of the mesh to the terminal.  
  Instead of printing the values in a human readable format, the state is serialized in json format. 
  This allows octoprint plugins to easily capture the data and generate height maps approximating the bed's surface.
- `BED_MESH_CLEAR`: This command clears the mesh and removes all z adjustment.  
  It is recommended to put this in your end-gcode.
- `BED_MESH_PROFILE LOAD=<name> SAVE=<name> REMOVE=<name>`: This command provides profile management for mesh state.  
  LOAD will restore the mesh state from the profile matching the supplied name. 
  SAVE will save the current mesh state to a profile matching the supplied name.  
  Remove will delete the profile matching the supplied name from persistent memory.  
  Note that after SAVE or REMOVE operations have been run the SAVE_CONFIG gcode must be run to make the changes to peristent memory permanent.
- `BED_MESH_OFFSET [X=<value>] [Y=<value>]`:  Applies X and/or Y offsets to the mesh lookup.  
  This is useful for printers with independent extruders, as an offset is necessary to produce correct Z adjustment after a tool change.

### Bed Screws Helper

The following commands are available when the [bed_screws config section](Config_Reference.md#bed_screws) is enabled (also see the [manual level guide](Manual_Level.md#adjusting-bed-leveling-screws)):
- `BED_SCREWS_ADJUST`: This command will invoke the bed screws adjustment tool. 
  It will command the nozzle to different locations (as defined in the config file) and allow one to make adjustments to the bed screws so that the bed is a constant distance from the nozzle.

### Bed Screws Tilt Adjust Helper

The following commands are available when the [screws_tilt_adjust config section](Config_Reference.md#screws_tilt_adjust) is enabled (also see the [manual level guide](Manual_Level.md#adjusting-bed-leveling-screws-using-the-bed-probe)):
- `SCREWS_TILT_CALCULATE [DIRECTION=CW|CCW] [<probe_parameter>=<value>]`: This command will invoke the bed screws adjustment tool. 
  It will command the nozzle to different locations (as defined in the config file) probing the z height and calculate the number of knob turns to adjust the bed level. 
  If DIRECTION is specified, the knob turns will all be in the same direction, clockwise (CW) or counterclockwise (CCW). 
  See the PROBE command for details on the optional probe parameters.
  IMPORTANT: You MUST always do a G28 before using this command.

### Z Tilt

The following commands are available when the [z_tilt config section](Config_Reference.md#z_tilt) is enabled:
- `Z_TILT_ADJUST [<probe_parameter>=<value>]`: This command will probe the points specified in the config and then make independent adjustments to each Z stepper to compensate for tilt. 
  See the PROBE command for details on the optional probe parameters.

### Dual Carriages

The following command is available when the zdual_carriage config section](Config_Reference.md#dual_carriage) is enabled:
- `SET_DUAL_CARRIAGE CARRIAGE=[0|1]`: This command will set the active carriage. It is typically invoked from the activate_gcode and deactivate_gcode fields in a multiple extruder configuration.

### TMC stepper drivers

The following commands are available when any of the [tmcXXXX config sections](Config_Reference.md#tmc-stepper-driver-configuration) are enabled:
- `DUMP_TMC STEPPER=<name>`: This command will read the TMC driver registers and report their values.
- `INIT_TMC STEPPER=<name>`: This command will intitialize the TMC registers. Needed to re-enable the driver if power to the chip is turned off then back on.
- `SET_TMC_CURRENT STEPPER=<name> CURRENT=<amps> HOLDCURRENT=<amps>`: This will adjust the run and hold currents of the TMC driver.
  (HOLDCURRENT is not applicable to tmc2660 drivers.)
- `SET_TMC_FIELD STEPPER=<name> FIELD=<field> VALUE=<value>`: This will alter the value of the specified register field of the TMC driver. 
  This command is intended for low-level diagnostics and debugging only because changing the fields during run-time can lead to undesired and potentially dangerous behavior of your printer.
  Permanent changes should be made using the printer configuration file instead. 
  No sanity checks are performed for the given values.

### Endstop adjustments by stepper phase

The following commands are available when an [endstop_phase config section](Config_Reference.md#endstop_phase) is enabled (also see the [endstop phase guide](Endstop_Phase.md)):
- `ENDSTOP_PHASE_CALIBRATE [STEPPER=<config_name>]`: If no STEPPER parameter is provided then this command will reports statistics on endstop stepper phases during past homing operations. 
  When a STEPPER parameter is provided it arranges for the given endstop phase setting to be written to the config file (in conjunction with the SAVE_CONFIG command).

### Force movement

The following commands are available when the [force_move config section](Config_Reference.md#force_move) is enabled:
- `FORCE_MOVE STEPPER=<config_name> DISTANCE=<value> VELOCITY=<value> [ACCEL=<value>]`: This command will forcibly move the given stepper the given distance (in mm) at the given constant velocity (in mm/s). 
  If ACCEL is specified and is greater than zero, then the  given acceleration (in mm/s^2) will be used; otherwise no acceleration is performed. No boundary checks are performed; 
  no kinematic updates are made; 
  other parallel steppers on an axis will not be moved. 
  Use caution as an incorrect command could cause damage! 
  Using this command will almost certainly place the low-level kinematics in an incorrect state; 
  issue a G28 afterwards to reset the kinematics. 
  This command is intended for low-level diagnostics and debugging.
- `SET_KINEMATIC_POSITION [X=<value>] [Y=<value>] [Z=<value>]`: Force the low-level kinematic code to believe the toolhead is at the given cartesian position. 
  This is a diagnostic and debugging command; 
  use SET_GCODE_OFFSET and/or G92 for regular axis transformations. 
  If an axis is not specified then it will default to the position that the head was last commanded to. 
  Setting an incorrect or invalid position may lead to internal software errors. 
  This command may invalidate future boundary checks; issue a G28 afterwards to reset the kinematics.

### SDcard loop

When the [sdcard_loop config section](Config_Reference.md#sdcard_loop) is enabled, the following extended commands are available:
- `SDCARD_LOOP_BEGIN COUNT=<count>`: Begin a looped section in the SD print. A count of 0 indicates that the section should be looped indefinately.
- `SDCARD_LOOP_END`: End a looped section in the SD print.
- `SDCARD_LOOP_DESIST`: Complete existing loops without further iterations.

### Send message (respond) to host

The following commands are availabe when the [respond config section](Config_Reference.md#respond) is enabled.
- `M118 <message>`: echo the message prepended with the configured default prefix (or `echo: ` if no prefix is configured).
- `RESPOND MSG="<message>"`: echo the message prepended with the configured default prefix (or `echo: ` if no prefix is configured).
- `RESPOND TYPE=echo MSG="<message>"`: echo the message prepended with `echo: `.
- `RESPOND TYPE=command MSG="<message>"`: echo the message prepended with `// `.  Octopint can be configured to respond to these messages (e.g.  `RESPOND TYPE=command MSG=action:pause`).
- `RESPOND TYPE=error MSG="<message>"`: echo the message prepended with `!! `.
- `RESPOND PREFIX=<prefix> MSG="<message>"`: echo the message prepended with `<prefix>`. (The `PREFIX` parameter will take priority over the `TYPE` parameter)

### Pause Resume

The following commands are available when the [pause_resume config section](Config_Reference.md#pause_resume) is enabled:
- `PAUSE`: Pauses the current print. The current position is captured for restoration upon resume.
- `RESUME [VELOCITY=<value>]`: Resumes the print from a pause, first restoring the previously captured position.  The VELOCITY parameter determines the speed at which the tool should return to the original captured position.
- `CLEAR_PAUSE`: Clears the current paused state without resuming the print. This is useful if one decides to cancel a print after a PAUSE. 
  It is recommended to add this to your start gcode to make
  sure the paused state is fresh for each print.
- `CANCEL_PRINT`: Cancels the current print.

### Filament Sensor

The following command is available when the [filament_switch_sensor or filament_motion_sensor config section](Config_Reference.md#filament_switch_sensor) is enabled.
- `QUERY_FILAMENT_SENSOR SENSOR=<sensor_name>`: Queries the current status of the filament sensor. The data displayed on the terminal will depend on the sensor type defined in the confguration.
- `SET_FILAMENT_SENSOR SENSOR=<sensor_name> ENABLE=[0|1]`: Sets the filament sensor on/off. If ENABLE is set to 0, the filament sensor will be disabled, if set to 1 it is enabled.

### Firmware Retraction

The following commands are available when the [firmware_retraction config section](Config_Reference.md#firmware_retraction) is enabled. 
These commands allow you to utilise the firmware retraction feature available in many slicers, to reduce stringing during non-extrusion moves from one part of the print to another.
Appropriately configuring pressure advance reduces the length of retraction required.
- `SET_RETRACTION [RETRACT_LENGTH=<mm>] [RETRACT_SPEED=<mm/s>]
  [UNRETRACT_EXTRA_LENGTH=<mm>] [UNRETRACT_SPEED=<mm/s>]`: Adjust the parameters used by firmware retraction. RETRACT_LENGTH determines the length of filament to retract and unretract. 
  The speed of retraction is adjusted via RETRACT_SPEED, and is typically set relatively high. 
  The speed of unretraction is adjusted via UNRETRACT_SPEED, and is not particularly critical, although often lower than RETRACT_SPEED. In some cases it is useful to add a small amount of additional length on unretraction, and this is set via UNRETRACT_EXTRA_LENGTH. SET_RETRACTION is commonly set as part of slicer per-filament configuration, as different filaments require different parameter settings.
- `GET_RETRACTION`: Queries the current parameters used by firmware retraction and displays them on the terminal.
- `G10`: Retracts the extruder using the currently configured parameters.
- `G11`: Unretracts the extruder using the currently configured parameters.

### Skew Correction

The following commands are available when the [skew_correction config section](Config_Reference.md#skew_correction) is enabled (also see the [skew correction guide](skew_correction.md)):
- `SET_SKEW [XY=<ac_length,bd_length,ad_length>] [XZ=<ac,bd,ad>]
  [YZ=<ac,bd,ad>] [CLEAR=<0|1>]`: Configures the [skew_correction]
  module with measurements (in mm) taken from a calibration print.
  One may enter measurements for any combination of planes, planes not entered will retain their current value. If `CLEAR=1` is entered then all skew correction will be disabled.
- `GET_CURRENT_SKEW`: Reports the current printer skew for each plane in both radians and degrees. The skew is calculated based on parameters provided via the `SET_SKEW` gcode.
- `CALC_MEASURED_SKEW [AC=<ac_length>] [BD=<bd_length>]
  [AD=<ad_length>]`: Calculates and reports the skew (in radians and degrees) based on a measured print. This can be useful for determining the printer's current skew after correction has been applied. 
  It may also be useful before correction is applied to determine if skew correction is necessary. See skew_correction.md for details on skew calibration objects and measurements.
- `SKEW_PROFILE [LOAD=<name>] [SAVE=<name>] [REMOVE=<name>]`: Profile management for skew_correction. LOAD will restore skew state from the profile matching the supplied name. 
  SAVE will save the current skew state to a profile matching the supplied name. 
  Remove will delete the profile matching the supplied name from persistent memory. 
  Note that after SAVE or REMOVE operations have been run the SAVE_CONFIG gcode must be run to make the changes to peristent memory permanent.

### Delayed GCode

The following command is enabled if a [delayed_gcode config section](Config_Reference.md#delayed_gcode) has been enabled (also see the [template guide](Command_Templates.md#delayed-gcodes)):
- `UPDATE_DELAYED_GCODE [ID=<name>] [DURATION=<seconds>]`:  Updates the delay duration for the identified [delayed_gcode] and starts the timer for gcode execution.  A value of 0 will cancel a pending delayed gcode from executing.

### Save Variables

The following command is enabled if a [save_variables config section](Config_Reference.md#save_variables) has been enabled:
- `SAVE_VARIABLE VARIABLE=<name> VALUE=<value>`: Saves the variable to disk so that it can be used across restarts. All stored variables are loaded into the `printer.save_variables.variables` dict at startup and can be used in gcode macros. The provided VALUE is parsed as a Python literal.

### Resonance compensation

The following command is enabled if an [input_shaper config section](Config_Reference.md#input_shaper) has been enabled (also see the [resonance compensation guide](Resonance_Compensation.md)):
- `SET_INPUT_SHAPER [SHAPER_FREQ_X=<shaper_freq_x>]
  [SHAPER_FREQ_Y=<shaper_freq_y>]
  [DAMPING_RATIO_X=<damping_ratio_x>]
  [DAMPING_RATIO_Y=<damping_ratio_y>] [SHAPER_TYPE=<shaper>]
  [SHAPER_TYPE_X=<shaper_type_x>] [SHAPER_TYPE_Y=<shaper_type_y>]`:
  Modify input shaper parameters. Note that SHAPER_TYPE parameter resets input shaper for both X and Y axes even if different shaper types have been configured in [input_shaper] section. SHAPER_TYPE cannot be used together with either of SHAPER_TYPE_X and SHAPER_TYPE_Y parameters. 
  See [config reference](Config_Reference.md#input_shaper) for more details on each of these parameters.

### Temperature Fan Commands

The following command is available when a [temperature_fan config section](Config_Reference.md#temperature_fan) is enabled:
- `SET_TEMPERATURE_FAN_TARGET temperature_fan=<temperature_fan_name>
  [target=<target_temperature>] [min_speed=<min_speed>]  [max_speed=<max_speed>]`: Sets the target temperature for a temperature_fan. If a target is not supplied, it is set to the specified temperature in the config file. If speeds are not supplied, no change is applied.

### Adxl345 Accelerometer Commands

The following commands are available when an [adxl345 config section](Config_Reference.md#adxl345) is enabled:
- `ACCELEROMETER_MEASURE [CHIP=<config_name>] [RATE=<value>]
  [NAME=<value>]`: Starts accelerometer measurements at the requested number of samples per second. If CHIP is not specified it defaults to "default". Valid rates are 25, 50, 100, 200, 400, 800, 1600, and 3200. 
  The command works in a start-stop mode: when executed for the first time, it starts the measurements, next execution stops them. 
  If RATE is not specified, then the default value is used (either from `printer.cfg` or `3200` default value). The results of measurements are written to a file named `/tmp/adxl345-<chip>-<name>.csv` where `<chip>` is the name of the accelerometer chip (`my_chip_name` from `[adxl345 my_chip_name]`) and `<name>` is the optional NAME parameter. 
  If NAME is not specified it defaults to the current time in "YYYYMMDD_HHMMSS" format. 
  If the accelerometer does not have a name in its config section (simply `[adxl345]`) <chip> part of the name is not generated.
- `ACCELEROMETER_QUERY [CHIP=<config_name>] [RATE=<value>]`: queries accelerometer for the current value. If CHIP is not specified it defaults to "default". 
  If RATE is not specified, the default value is used. 
  This command is useful to test the connection to the ADXL345 accelerometer: one of the returned values should be a free-fall acceleration (+/- some noise of the chip).
- `ADXL345_DEBUG_READ [CHIP=<config_name>] REG=<register>`: queries ADXL345 register <register> (e.g. 44 or 0x2C). Can be useful for debugging purposes.
- `ADXL345_DEBUG_WRITE [CHIP=<config_name>] REG=<reg> VAL=<value>`:
  writes raw <value> into a register <register>. Both <value> and <register> can be a decimal or a hexadecimal integer. Use with care, and refer to ADXL345 data sheet for the reference.

### 공진 테스트 명령

[resonance_tester config section](Config_Reference.md#resonance_tester)이 
활성화된 경우 다음 명령을 사용할 수 있습니다 
([measuring resonances guide](Measuring_Resonances.md) 참조):
- `MEASURE_AXES_NOISE`: 활성화된 모든 가속도계 칩의 모든 축에 대한 
  노이즈를 측정하고 출력합니다.
- `TEST_RESONANCES AXIS=<axis> OUTPUT=<resonances,raw_data>
  [NAME=<name>] [FREQ_START=<min_freq>] [FREQ_END=<max_freq>]
  [HZ_PER_SEC=<hz_per_sec>] [INPUT_SHAPING=[<0:1>]]`: 요청한 <축>에 대해 구성된 
  모든 프로브 포인트에서 공진 테스트를 실행하고 각 축에 대해 구성된 가속도계 칩을 사용하여 
  가속도를 측정합니다. <축> 은 X 또는 Y가 될 수 있거나 임의의 방향을 `AXIS=dx,dy`로 
  지정할 수 있습니다. 여기서 dx 및 dy는 방향 벡터를 정의하는 부동 소수점 숫자입니다.
  (예: `AXIS=X`, `AXIS=Y`, 또는 대각선 방향을 정의하는 `AXIS=1,-1`).
  `AXIS=dx,dy`와 `AXIS=-dx,-dy`는 동일합니다. 'INPUT_SHAPING=0'이거나 설정되지 
  않은 경우(기본값), 입력 셰이퍼가 활성화된 상태에서 공진 테스트를 실행하는 것이 유효하지 
  않기 때문에 공진 테스트에 대한 입력 셰이핑을 비활성화합니다.
  'OUTPUT' 매개변수는 출력이 기록될 쉼표로 구분된 목록입니다.
  `raw_data`가 요청된 경우, 원시 가속도계 데이터가 파일 또는 일련의 파일 
  `/tmp/raw_data_<axis>_[<point>_]<name>.csv` 에 기록됩니다. (
  둘 이상의 프로브 포인트가 구성된 경우에만 생성되는 이름의 `<point>_` 부분).
  'resonances'가 지정된 경우, 주파수 응답이 계산되고(모든 프로브 포인트에서) 
  `/tmp/resonances_<axis>_<name>.csv` 파일에 기록됩니다. 설정하지 않으면 
  OUTPUT은 기본적으로 'resonances'로 설정되고 NAME은 "YYYYMMDD_HHMMSS" 
  형식의 현재 시간으로 기본 설정됩니다.
- `SHAPER_CALIBRATE [AXIS=<axis>] [NAME=<name>]
  [FREQ_START=<min_freq>] [FREQ_END=<max_freq>]
  [HZ_PER_SEC=<hz_per_sec>] [MAX_SMOOTHING=<max_smoothing>]`:
  `TEST_RESONANCES`와 유사하게 구성된 대로 공진 테스트를 실행하고 요청된 
  축(또는 `AXIS` 매개변수가 설정되지 않은 경우 X축과 Y축 모두)에 대한 입력 
  셰이퍼에 대한 최적의 매개변수를 찾으려고 시도합니다.
  `MAX_SMOOTHING`이 설정되지 않은 경우 해당 값은 `[resonance_tester]` 
  섹션에서 가져오고 기본값은 설정되지 않습니다. 이 기능의 사용에 대한 자세한 내용은 
  공진 측정 가이드의 [Max smoothing](Measuring_Resonances.md#max-smoothing)를 
  참조하세요. 조정 결과는 콘솔에 인쇄되고 주파수 응답과 다양한 입력 셰이퍼 값은 CSV 파일 
  `/tmp/calibration_data_<axis>_<name>.csv`에 기록됩니다. 지정하지 않는 한 
  NAME의 기본값은 "YYYYMMDD_HHMMSS" 형식의 현재 시간입니다.
  제안된 입력 셰이퍼 매개변수는 'SAVE_CONFIG' 명령을 실행하여 구성에 유지될 수 있습니다.

### 팔레트 2 명령

다음 명령은 [palette2 config section](Config_Reference.md#palette2)이 
활성화된 경우 사용할 수 있습니다:
- `PALETTE_CONNECT`: 이 명령은 팔레트 2와의 연결을 초기화합니다. 
- `PALETTE_DISCONNECT`: 이 명령은 팔레트 2에서 연결을 끊습니다. 
- `PALETTE_CLEAR`: 이 명령은 필라멘트의 모든 입력 및 출력 경로를 지우도록 팔레트 
  2에 지시합니다.
- `PALETTE_CUT`: 이 명령은 현재 스플라이스 코어에 로드된 필라멘트를 절단하도록 
  팔레트 2에 지시합니다.
- `PALETTE_SMART_LOAD`: 이 명령은 팔레트 2에서 스마트 로드 시퀀스를 시작합니다.
  Filament는 프린터용 장치에서 보정된 거리만큼 밀어내어 자동으로 Load되며, 
  Load가 완료되면 Palette 2에 지시합니다. 이 명령은 누르는 것과 동일합니다. 
  이 명령은 필라멘트 로드가 완료된 후 Palette 2 화면에서 직접 **Smart Load**를 
  누르는 것과 같습니다.

특수 OCode(Omega Codes)를 포함하여 팔레트 인쇄 작업
GCode 파일에서:
- `O1`...`O32`: 이 코드는 GCode 스트림에서 읽고 이 모듈에 의해 처리되고 
  팔레트 2 장치로 전달됩니다.
