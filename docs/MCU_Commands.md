# MCU 명령

이 문서는 Klipper "호스트" 소프트웨어에서 전송되고 Klipper 마이크로 컨트롤러 소프트웨어에 
의해 처리되는 저수준 마이크로 컨트롤러 명령에 대한 정보를 제공합니다. 
이 문서가 모든 MCU 명령어를 커버하지는 않습니다.

이 문서는 저수준 마이크로 컨트롤러 명령을 이해하는 데 관심이 있는 개발자에게 유용할 수 있습니다.


명령어 형식 및 전송에 대한 자세한 내용은 [프로토콜](Protocol.md) 문서를 참조하십시오. 
여기에 있는 명령은 "printf" 스타일 구문을 사용하여 설명됩니다. 
해당 형식에 익숙하지 않은 사용자를 위해 '%...' 시퀀스가 표시되는 경우 실제 정수로 대체되어야 합니다. 
예를 들어 "count=%c"가 있는 설명은 "count=10"이라는 텍스트로 대체될 수 있습니다. 
"열거"로 간주되는 매개변수 (위의 프로토콜 문서 참조) 는 마이크로 컨트롤러의 정수 값으로 자동 변환되는 
문자열 값을 사용합니다. 이것은 "pin" (또는 "\_pin" 접미사가 있는) 이라는 매개변수가 일반적입니다.

## 시작 명령

마이크로 컨트롤러 및 주변 장치를 구성하기 위해 특정 일회성 작업을 수행해야 할 수도 있습니다. 
이 섹션에서는 해당 용도로 사용할 수 있는 일반적인 명령을 나열합니다. 대부분의 마이크로 컨트롤러 
명령과 달리 이러한 명령은 수신되는 즉시 실행되며 특정 설정이 필요하지 않습니다.

일반적인 시작 명령:

* `set_digital_out pin=%u value=%c` : 이 명령은 주어진 핀을 디지털 출력 GPIO로 즉시 
  구성하고 이를 low level (값=0) 또는 high level (값=1)로 설정합니다. 이 명령은 LED의 
  초기 값을 구성하고 스테퍼 드라이버 마이크로 스테핑 핀의 초기 값을 구성하는 데 유용할 수 있습니다.
 
* `set_pwm_out pin=%u cycle_ticks=%u value=%hu` : 이 명령은 주어진 수의 cycle_ticks로 
  하드웨어 기반 PWM(펄스 폭 변조)을 사용하도록 지정된 핀을 즉시 구성합니다. "cycle_ticks"는 각 
  전원 켜기 및 전원 끄기 주기가 지속되어야 하는 MCU 클록 틱 수입니다. cycle_ticks 값 1은 가능한 
  가장 빠른 주기 시간을 요청하는 데 사용할 수 있습니다. "값" 매개변수는 0에서 255 사이이며 0은 
  완전히 꺼진 상태를 나타내고 255는 완전히 켜진 상태를 나타냅니다. 이 명령은 CPU 및 노즐 냉각 팬을 
  활성화하는 데 유용할 수 있습니다.

## 저수준 마이크로 컨트롤러 구성


마이크로 컨트롤러의 대부분의 명령은 성공적으로 호출되기 전에 초기 설정이 필요합니다. 이 섹션에서는 구성 
프로세스의 개요를 제공합니다. 이 섹션과 다음 섹션은 Klipper의 내부 세부 정보에 관심이 있는 개발자만 
관심을 가질 것입니다.

When the host first connects to the micro-controller it always starts by obtaining 
a data dictionary (see [protocol](Protocol.md) for more information). 
호스트가 마이크로 컨트롤러에 처음 연결할 때 항상 data dictionary를 얻는 것으로 시작합니다 (자세한 
내용은 [프로토콜](Protocol.md) 참조). data dictionary 을 얻은 후 호스트는 마이크로 컨트롤러가 
"configured" 상태인지 확인하고 그렇지 않은 경우 Configuration 합니다. Configuration 에는 
다음 단계가 포함됩니다.

* `get_config` : 호스트는 마이크로 컨트롤러가 이미 구성되어 있는지 확인하는 것으로 시작합니다. 
  마이크로 컨트롤러는 "config" 응답 메시지로 이 명령에 응답합니다. 마이크로 컨트롤러 소프트웨어는 
  전원을 켤 때 항상 configuration 되지 않은 상태로 시작됩니다. 호스트가 configuration 
  프로세스를 완료할 때까지 이 상태를 유지합니다 (finalize_config 명령으로 실행). 마이크로 
  컨트롤러가 이전 세션에서 이미 configuration 되어 있고 원하는 설정으로 구성된 경우 호스트에서 
  추가 작업이 필요하지 않으며 구성 프로세스가 성공적으로 종료됩니다.

* `allocate_oids count=%c` : 이 명령은 호스트에 필요한 최대 object-id (oid) 수를 마이크로 
  컨트롤러에 알리기 위해 실행됩니다. 이 명령은 한 번만 실행하는 것이 유효합니다. oid 는 각 스테퍼, 
  각 엔드스톱 및 예약 가능한 각 gpio 핀에 할당된 정수 식별자입니다. 호스트는 하드웨어를 작동하는 데 
  필요한 oid 수를 미리 결정하고 이를 마이크로 컨트롤러에 전달하여 oid 에서 내부 개체로의 매핑을 
  저장하기에 충분한 메모리를 할당할 수 있습니다.

* `config_XXX oid=%c ...` : 전통적으로 "config_" 접두사로 시작하는 모든 명령은 새 마이크로 
  컨트롤러 개체를 만들고 지정된 oid 를 할당합니다. 예를 들어 config_digital_out 명령은 지정된 
  핀을 디지털 출력 GPIO 로 구성하고 호스트가 주어진 GPIO 에 대한 변경을 예약하는데 사용할 수 있는 
  내부 개체를 생성합니다. config 명령에 전달된 oid 매개변수는 호스트에 의해 선택되며 0 과 
  assign_oids 명령에 제공된 최대 개수 사이에 있어야 합니다. config 명령은 마이크로 컨트롤러가 
  구성된 상태가 아닐 때 (즉, 호스트가 finalize_config를 전송하기 전) 및 assign_oids 명령이 
  전송된 후에만 실행할 수 있습니다.

* `finalize_config crc=%u` : finalize_config 명령은 마이크로 컨트롤러를 구성되지 않은 
  상태에서 구성된 상태로 전환합니다. 마이크로 컨트롤러에 전달된 crc 매개변수는 저장되고 "config" 
  응답 메시지로 호스트에 다시 제공됩니다. 관례에 따라 호스트는 요청할 구성의 32비트 CRC를 가져오고 
  후속 통신 세션이 시작될 때 마이크로 컨트롤러에 저장된 CRC가 원하는 CRC와 정확히 일치하는지 
  확인합니다. CRC가 일치하지 않으면 호스트는 마이크로 컨트롤러가 호스트가 원하는 상태로 구성되지 
  않았음을 알고 있습니다.

### 일반적인 마이크로 컨트롤러 오브젝트

This section lists some commonly used config commands.

* `config_digital_out oid=%c pin=%u value=%c default_value=%c
  max_duration=%u` : This command creates an internal micro-controller
  object for the given GPIO 'pin'. The pin will be configured in
  digital output mode and set to an initial value as specified by
  'value' (0 for low, 1 for high). Creating a digital_out object
  allows the host to schedule GPIO updates for the given pin at
  specified times (see the queue_digital_out command described below).
  Should the micro-controller software go into shutdown mode then all
  configured digital_out objects will be set to 'default_value'. The
  'max_duration' parameter is used to implement a safety check - if it
  is non-zero then it is the maximum number of clock ticks that the
  host may set the given GPIO to a non-default value without further
  updates. For example, if the default_value is zero and the
  max_duration is 16000 then if the host sets the gpio to a value of
  one then it must schedule another update to the gpio pin (to either
  zero or one) within 16000 clock ticks. This safety feature can be
  used with heater pins to ensure the host does not enable the heater
  and then go off-line.

* `config_pwm_out oid=%c pin=%u cycle_ticks=%u value=%hu
  default_value=%hu max_duration=%u` : This command creates an
  internal object for hardware based PWM pins that the host may
  schedule updates for. Its usage is analogous to config_digital_out -
  see the description of the 'set_pwm_out' and 'config_digital_out'
  commands for parameter description.

* `config_analog_in oid=%c pin=%u` : This command is used to configure
  a pin in analog input sampling mode. Once configured, the pin can be
  sampled at regular interval using the query_analog_in command (see
  below).

* `config_stepper oid=%c step_pin=%c dir_pin=%c invert_step=%c` : This
  command creates an internal stepper object. The 'step_pin' and
  'dir_pin' parameters specify the step and direction pins
  respectively; this command will configure them in digital output
  mode. The 'invert_step' parameter specifies whether a step occurs on
  a rising edge (invert_step=0) or falling edge (invert_step=1).

* `config_endstop oid=%c pin=%c pull_up=%c stepper_count=%c` : This
  command creates an internal "endstop" object. It is used to specify
  the endstop pins and to enable "homing" operations (see the
  endstop_home command below). The command will configure the
  specified pin in digital input mode. The 'pull_up' parameter
  determines whether hardware provided pullup resistors for the pin
  (if available) will be enabled. The 'stepper_count' parameter
  specifies the maximum number of steppers that this endstop may need
  to halt during a homing operation (see endstop_home below).

* `config_spi oid=%c bus=%u pin=%u mode=%u rate=%u shutdown_msg=%*s` :
  This command creates an internal SPI object. It is used with
  spi_transfer and spi_send commands (see below).  The "bus"
  identifies the SPI bus to use (if the micro-controller has more than
  one SPI bus available). The "pin" specifies the chip select (CS) pin
  for the device. The "mode" is the SPI mode (should be between 0 and
  3). The "rate" parameter specifies the SPI bus rate (in cycles per
  second). Finally, the "shutdown_msg" is an SPI command to send to
  the given device should the micro-controller go into a shutdown
  state.

* `config_spi_without_cs oid=%c bus=%u mode=%u rate=%u
  shutdown_msg=%*s` : This command is similar to config_spi, but
  without a CS pin definition. It is useful for SPI devices that do
  not have a chip select line.

## 일반적인 명령

This section lists some commonly used run-time commands. It is likely
only of interest to developers looking to gain insight into Klipper.

* `set_digital_out_pwm_cycle oid=%c cycle_ticks=%u` : This command
  configures a digital output pin (as created by config_digital_out)
  to use "software PWM". The 'cycle_ticks' is the number of clock
  ticks for the PWM cycle. Because the output switching is implemented
  in the micro-controller software, it is recommended that
  'cycle_ticks' correspond to a time of 10ms or greater.

* `queue_digital_out oid=%c clock=%u on_ticks=%u` : This command will
  schedule a change to a digital output GPIO pin at the given clock
  time. To use this command a 'config_digital_out' command with the
  same 'oid' parameter must have been issued during micro-controller
  configuration. If 'set_digital_out_pwm_cycle' has been called then
  'on_ticks' is the on duration (in clock ticks) for the pwm cycle.
  Otherwise, 'on_ticks' should be either 0 (for low voltage) or 1 (for
  high voltage).

* `queue_pwm_out oid=%c clock=%u value=%hu` : Schedules a change to a
  hardware PWM output pin. See the 'queue_digital_out' and
  'config_pwm_out' commands for more info.

* `query_analog_in oid=%c clock=%u sample_ticks=%u sample_count=%c
  rest_ticks=%u min_value=%hu max_value=%hu` : This command sets up a
  recurring schedule of analog input samples. To use this command a
  'config_analog_in' command with the same 'oid' parameter must have
  been issued during micro-controller configuration. The samples will
  start as of 'clock' time, it will report on the obtained value every
  'rest_ticks' clock ticks, it will over-sample 'sample_count' number
  of times, and it will pause 'sample_ticks' number of clock ticks
  between over-sample samples. The 'min_value' and 'max_value'
  parameters implement a safety feature - the micro-controller
  software will verify the sampled value (after any oversampling) is
  always between the supplied range. This is intended for use with
  pins attached to thermistors controlling heaters - it can be used to
  check that a heater is within a temperature range.

* `get_clock` : This command causes the micro-controller to generate a
  "clock" response message. The host sends this command once a second
  to obtain the value of the micro-controller clock and to estimate
  the drift between host and micro-controller clocks. It enables the
  host to accurately estimate the micro-controller clock.

### 스테퍼 명령

* `queue_step oid=%c interval=%u count=%hu add=%hi` : This command
  schedules 'count' number of steps for the given stepper, with
  'interval' number of clock ticks between each step. The first step
  will be 'interval' number of clock ticks since the last scheduled
  step for the given stepper. If 'add' is non-zero then the interval
  will be adjusted by 'add' amount after each step. This command
  appends the given interval/count/add sequence to a per-stepper
  queue. There may be hundreds of these sequences queued during normal
  operation. New sequence are appended to the end of the queue and as
  each sequence completes its 'count' number of steps it is popped
  from the front of the queue. This system allows the micro-controller
  to queue potentially hundreds of thousands of steps - all with
  reliable and predictable schedule times.

* `set_next_step_dir oid=%c dir=%c` : This command specifies the value
  of the dir_pin that the next queue_step command will use.

* `reset_step_clock oid=%c clock=%u` : Normally, step timing is
  relative to the last step for a given stepper. This command resets
  the clock so that the next step is relative to the supplied 'clock'
  time. The host usually only sends this command at the start of a
  print.

* `stepper_get_position oid=%c` : This command causes the
  micro-controller to generate a "stepper_position" response message
  with the stepper's current position. The position is the total
  number of steps generated with dir=1 minus the total number of steps
  generated with dir=0.

* `endstop_home oid=%c clock=%u sample_ticks=%u sample_count=%c
  rest_ticks=%u pin_value=%c` : This command is used during stepper
  "homing" operations. To use this command a 'config_endstop' command
  with the same 'oid' parameter must have been issued during
  micro-controller configuration. When this command is invoked, the
  micro-controller will sample the endstop pin every 'rest_ticks'
  clock ticks and check if it has a value equal to 'pin_value'. If the
  value matches (and it continues to match for 'sample_count'
  additional samples spread 'sample_ticks' apart) then the movement
  queue for the associated stepper will be cleared and the stepper
  will come to an immediate halt. The host uses this command to
  implement homing - the host instructs the endstop to sample for the
  endstop trigger and then it issues a series of queue_step commands
  to move a stepper towards the endstop. Once the stepper hits the
  endstop, the trigger will be detected, the movement halted, and the
  host notified.

### Move queue

Each queue_step command utilizes an entry in the micro-controller
"move queue". This queue is allocated when it receives the
"finalize_config" command, and it reports the number of available
queue entries in "config" response messages.

It is the responsibility of the host to ensure that there is available
space in the queue before sending a queue_step command. The host does
this by calculating when each queue_step command completes and
scheduling new queue_step commands accordingly.

### SPI 명령

* `spi_transfer oid=%c data=%*s` : This command causes the
  micro-controller to send 'data' to the spi device specified by 'oid'
  and it generates a "spi_transfer_response" response message with the
  data returned during the transmission.

* `spi_send oid=%c data=%*s` : This command is similar to
  "spi_transfer", but it does not generate a "spi_transfer_response"
  message.
