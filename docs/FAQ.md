# Frequently asked questions

1. [어떻게 프로젝트에 기부할수 있나요?](#어떻게-프로젝트에-기부할수-있나요)
2. [rotation_distance는 어떻게 계산합니까?](#rotation_distance는-어떻게-계산합니까)
3. [내 시리얼 포트는 어디에 있습니까?](#내-시리얼-포트는-어디에-있습니까)
4. [마이크로 컨트롤러가 다시 시작되면 장치가 /dev/ttyUSB1 로 변경됩니다](#마이크로-컨트롤러가-다시-시작되면-장치가-devttyUSB1-로-변경됩니다)
5. ["make flash" 가 동작하지 않습니다](#make-flash-가-동작하지-않습니다)
6. [시리얼 포트 전송 속도는 어떻게 변경합니까?](#시리얼-포트-전송-속도는-어떻게-변경합니까)
7. [Raspberry Pi 3 이외의 다른 기기에서 Klipper를 실행할 수 있습니까?](#Raspberry-Pi-3-이외의-다른-기기에서-Klipper를-실행할-수-있습니까)
8. [동일한 호스트 시스템에서 여러 Klipper 인스턴스를 실행할 수 있습니까?](#동일한-호스트-시스템에서-여러-Klipper-인스턴스를-실행할-수-있습니까)
9. [OctoPrint를 사용해야 합니까?](#OctoPrint를-사용해야-합니까)
10. [프린터를 원점으로 이동하기 전에 스테퍼를 이동할 수 없는 이유는 무엇입니까?](#프린터를-원점으로-이동하기-전에-스테퍼를-이동할-수-없는-이유는-무엇입니까)
11. [기본 설정에서 Z position_endstop이 0.5로 설정된 이유는 무엇입니까?](#기본-설정에서-Z-position_endstop이-0.5로-설정된-이유는-무엇입니까)
12. [Marlin에서 설정을 가져왔는데 X/Y 축은 잘 작동하지만 Z 축을 원점 복귀할 때 삐걱거리는 소리가 납니다](#Marlin에서-설정을-가져왔는데-XY-축은-잘-작동하지만-Z-축을-원점-복귀할-때-삐걱거리는-소리가-납니다)
13. [TMC 모터 드라이버가 인쇄 도중에 꺼집니다](#TMC-모터-드라이버가-인쇄-도중에-꺼집니다)
14. [랜덤하게 "MCU와의 통신 끊김" 오류가 계속 발생합니다](#랜덤하게-MCU와의-통신-끊김-오류가-계속-발생합니다)
15. [Raspberry Pi가 인쇄 중에 계속 재부팅됩니다](#Raspberry-Pi가-인쇄-중에-계속-재부팅됩니다.)
16. ["restart_method=command"를 설정하면 AVR 장치가 다시 시작할 때 멈춥니다](#restart_method=command를-설정하면-AVR-장치가-다시-시작할-때-멈춥니다)
17. [Raspberry Pi가 비정상 종료하면 히터가 켜져 있습니까?](#Raspberry-Pi가-비정상-종료하면-히터가-켜져-있습니까)
18. [Marlin 핀 번호를 Klipper 핀 이름으로 어떻게 변환합니까?](#Marlin-핀-번호를-Klipper-핀-이름으로-어떻게-변환합니까)
19. [내 장치를 특정 유형의 마이크로 컨트롤러 핀에 연결해야 합니까?](#내-장치를-특정-유형의-마이크로-컨트롤러-핀에-연결해야-합니까)
20. [M109/M190 "wait for temperature" 요청을 어떻게 취소합니까?](#M109/M190-wait-for-temperature-요청을-어떻게-취소합니까)
21. [프린터 탈조를 어떻게 알 수 있습니까?](#프린터-탈조를-어떻게-알-수-있습니까)
22. [Klipper가 "I lost my print" 오류를 보고하는 이유는 무엇입니까?](#Klipper가-I-lost-my-print-오류를-보고하는-이유는-무엇입니까)
23. [최신 소프트웨어로 업그레이드하려면 어떻게 합니까?](#최신-소프트웨어로-업그레이드하려면-어떻게-합니까)
24. [Klipper를 어떻게 제거합니까?](#Klipper를-어떻게-제거합니까)

## 어떻게 프로젝트에 기부할수 있나요?

감사합니다. Kevin의 Patreon 페이지는 다음과 같습니다.:
[https://www.patreon.com/koconnor](https://www.patreon.com/koconnor)

## rotation_distance는 어떻게 계산합니까?

이 문서를 [rotation distance document](Rotation_Distance.md) 보세요.

## 내 시리얼 포트는 어디에 있습니까?

The general way to find a USB serial port is to run `ls
/dev/serial/by-id/*` from an ssh terminal on the host machine. It will
likely produce output similar to the following:

USB 직렬 포트를 찾는 일반적인 방법은 호스트 시스템의 ssh 터미널에서 다음의 명령을 실행하면 됩니다. `ls
 /dev/serial/by-id/*`. 그럼 다음과 같이 나옵니다:
```
/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
```

The name found in the above command is stable and it is possible to
use it in the config file and while flashing the micro-controller
code. For example, a flash command might look similar to:
위 명령에서 찾은 이름을 사용해서 config 파일에 적을 수도 있고 마이크로 컨트롤러에 펌웨어 업로드 할때도 사용됩니다.
예를 들어 flash 명령은 다음과 같이 사용할 수 있습니다.
```
sudo service klipper stop
make flash FLASH_DEVICE=/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
sudo service klipper start
```
그리고 config 파일을 보면 아래와 같이 mcu 항목이 작성되어 있습니다:
```
[mcu]
serial: /dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
```

그곳에 ls 명령어로 찾은 시리얼 내용을 복사하여 넣으십시오. 내용은 프린터에 따라 다를 수 있습니다.

여러 개의 마이크로컨트롤러를 사용하고 있는데 마이크로컨트롤러의 고유 ID(`ls '/dev/serial/by-id/`)가 없는 경우에는 대신 `ls /dev/serial/by-path/*` 명령을 사용하여 위의 지침을 따르십시오.

## 마이크로 컨트롤러가 다시 시작되면 장치가 /dev/ttyUSB1 로 변경됩니다

링크의 지침을 따르면
"[내 시리얼 포트는 어디에 있습니까?](#내-시리얼-포트는-어디에-있습니까)" 문제 해결이 가능합니다.

## "make flash" 가 동작하지 않습니다

"make flash" 명령어는 가장 일반적인 방법을 사용하여 펌웨어 업로드를 시도합니다. 불행히도 각 플랫폼마다 펌웨어 업로드하는 방법에는 많은 차이가 있습니다. 따라서 "make flash" 명령이 모든 보드에서 작동하지 않을 수 있습니다.

만약 간헐적인 오류가 발생하거나 표준 설정을 잘 따르고 있다면, 펌웨어 업로드는 하는 동안 Klipper가 실행되고 있지 않은지 다시 확인하십시오 (sudo service klipper stop), 또한 OctoPrint가 실행되고 있는지도 확인하십시오 (Octoprint 프린터 연결 해제를 클릭합니다) 그리고 나서 FLASH_DEVICE가 보드에 대해 올바르게 설정되어 있는지 확인하십시오 (참조 [위 질문](#내-시리얼-포트는-어디에-있습니까)).

그러나 "make flash"가 보드에서 작동하지 않으면 수동으로 플래시해야 합니다. 펌웨어 업로드에 대한 특정 명령어가 포함된 구성 파일이 config 디렉토리에 있는지 확인하십시오. 또한 보드 제조업체의 설명서에 장치에 펌웨어 업로드 하는 방법이 설명되어 있는지 확인하십시오. 마지막으로 "avrdude" 또는 "bossac"과 같은 도구를 사용하여 장치에 수동으로 펌웨어 업로드 할 수 있습니다. 추가 정보는 [bootloader document](Bootloaders.md)를 참조하십시오.

## 시리얼 포트 전송 속도는 어떻게 변경합니까?

Klipper의 권장 전송 속도는 250000입니다. 이 전송 속도는 Klipper가 지원하는 모든 마이크로 컨트롤러 보드에서 잘 작동합니다. 다른 전송 속도를 권장하는 온라인 가이드를 찾은 경우 가이드의 해당 부분을 무시하고 기본값 250000을 계속 사용하십시오.

어쨌든 전송 속도를 변경하려면 마이크로 컨트롤러에서 새 속도를 구성해야 하고(**make menuconfig**를 통해서) 업데이트된 코드를 컴파일하고 마이크로 컨트롤러에 펌웨어 업로드 해야 합니다. Klipper printer.cfg 파일도 해당 전송 속도와 일치하도록 업데이트해야 합니다(자세한 내용은 [config reference](Config_Reference.md#mcu) 참조). 예를 들어:
```
[mcu]
baud: 250000
```

OctoPrint 웹 페이지에 표시된 전송 속도는 내부 Klipper 마이크로 컨트롤러 전송 속도에 영향을 미치지 않습니다. Klipper를 사용할 때는 항상 OctoPrint 전송 속도를 250000으로 설정하십시오.

Klipper 마이크로 컨트롤러 전송 속도는 마이크로 컨트롤러 부트로더의 전송 속도와 관련이 없습니다. 부트로더에 대한 추가 정보는 [bootloader document](Bootloaders.md)를 참조하십시오.


## Raspberry Pi 3 이외의 다른 기기에서 Klipper를 실행할 수 있습니까?

The recommended hardware is a Raspberry Pi 2, Raspberry Pi 3, or
Raspberry Pi 4.

Klipper will run on a Raspberry Pi 1 and on the Raspberry Pi Zero, but
these boards don't have enough processing power to run OctoPrint
well. It is common for print stalls to occur on these slower machines
when printing directly from OctoPrint. (The printer may move faster
than OctoPrint can send movement commands.) If you wish to run on one
one of these slower boards anyway, consider using the "virtual_sdcard"
feature when printing (see
[config reference](Config_Reference.md#virtual_sdcard) for details).

For running on the Beaglebone, see the
[Beaglebone specific installation instructions](beaglebone.md).

Klipper has been run on other machines. The Klipper host software only
requires Python running on a Linux (or similar) computer. However, if
you wish to run it on a different machine you will need Linux admin
knowledge to install the system prerequisites for that particular
machine. See the [install-octopi.sh](../scripts/install-octopi.sh)
script for further information on the necessary Linux admin steps.

If you are looking to run the Klipper host software on a low-end chip,
then be aware that, at a minimum, a machine with "double precision
floating point" hardware is required.

If you are looking to run the Klipper host software on a shared
general-purpose desktop or server class machine, then note that
Klipper has some real-time scheduling requirements. If, during a
print, the host computer also performs an intensive general-purpose
computing task (such as defragmenting a hard drive, 3d rendering,
heavy swapping, etc.), then it may cause Klipper to report print
errors.

Note: If you are not using an OctoPi image, be aware that several
Linux distributions enable a "ModemManager" (or similar) package that
can disrupt serial communication. (Which can cause Klipper to report
seemingly random "Lost communication with MCU" errors.) If you install
Klipper on one of these distributions you may need to disable that
package.

## 동일한 호스트 시스템에서 여러 Klipper 인스턴스를 실행할 수 있습니까?

It is possible to run multiple instances of the Klipper host software,
but doing so requires Linux admin knowledge. The Klipper installation
scripts ultimately cause the following Unix command to be run:
```
~/klippy-env/bin/python ~/klipper/klippy/klippy.py ~/printer.cfg -l /tmp/klippy.log
```
One can run multiple instances of the above command as long as each
instance has its own printer config file, its own log file, and its
own pseudo-tty. For example:
```
~/klippy-env/bin/python ~/klipper/klippy/klippy.py ~/printer2.cfg -l /tmp/klippy2.log -I /tmp/printer2
```

If you choose to do this, you will need to implement the necessary
start, stop, and installation scripts (if any). The
[install-octopi.sh](../scripts/install-octopi.sh) script and the
[klipper-start.sh](../scripts/klipper-start.sh) script may be useful
as examples.

## OctoPrint를 사용해야 합니까?

The Klipper software is not dependent on OctoPrint. It is possible to
use alternative software to send commands to Klipper, but doing so
requires Linux admin knowledge.

Klipper creates a "virtual serial port" via the "/tmp/printer" file,
and it emulates a classic 3d-printer serial interface via that file.
In general, alternative software may work with Klipper as long as it
can be configured to use "/tmp/printer" for the printer serial port.

## 프린터를 원점으로 이동하기 전에 스테퍼를 이동할 수 없는 이유는 무엇입니까?

The code does this to reduce the chance of accidentally commanding the
head into the bed or a wall. Once the printer is homed the software
attempts to verify each move is within the position_min/max defined in
the config file. If the motors are disabled (via an M84 or M18
command) then the motors will need to be homed again prior to
movement.

If you want to move the head after canceling a print via OctoPrint,
consider changing the OctoPrint cancel sequence to do that for
you. It's configured in OctoPrint via a web browser under:
Settings->GCODE Scripts

If you want to move the head after a print finishes, consider adding
the desired movement to the "custom g-code" section of your slicer.

If the printer requires some additional movement as part of the homing
process itself (or fundamentally does not have a homing process) then
consider using a safe_z_home or homing_override section in the config
file. If you need to move a stepper for diagnostic or debugging
purposes then consider adding a force_move section to the config
file. See [config reference](Config_Reference.md#customized_homing)
for further details on these options.

## 기본 설정에서 Z position_endstop이 0.5로 설정된 이유는 무엇입니까

For cartesian style printers the Z position_endstop specifies how far
the nozzle is from the bed when the endstop triggers. If possible, it
is recommended to use a Z-max endstop and home away from the bed (as
this reduces the potential for bed collisions). However, if one must
home towards the bed then it is recommended to position the endstop so
it triggers when the nozzle is still a small distance away from the
bed. This way, when homing the axis, it will stop before the nozzle
touches the bed. See the [bed level document](Bed_Level.md) for more
information.

## Marlin에서 설정을 가져왔는데 X/Y 축은 잘 작동하지만 Z 축을 원점 복귀할 때 삐걱거리는 소리가 납니다

Short answer: First, make sure you have verified the stepper
configuration as described in the
[config check document](Config_checks.md). If the problem persists,
try reducing the max_z_velocity setting in the printer config.

Long answer: In practice Marlin can typically only step at a rate of
around 10000 steps per second. If it is requested to move at a speed
that would require a higher step rate then Marlin will generally just
step as fast as it can. Klipper is able to achieve much higher step
rates, but the stepper motor may not have sufficient torque to move at
a higher speed. So, for a Z axis with a high gearing ratio or high
microsteps setting the actual obtainable max_z_velocity may be smaller
than what is configured in Marlin.

## TMC 모터 드라이버가 인쇄 도중에 꺼집니다

If using the TMC2208 (or TMC2224) driver in "standalone mode" then
make sure to use the
[latest version of Klipper](#how-do-i-upgrade-to-the-latest-software). A
workaround for a TMC2208 "stealthchop" driver problem was added to
Klipper in mid-March of 2020.

## 랜덤하게 "MCU와의 통신 끊김" 오류가 계속 발생합니다

This is commonly caused by hardware errors on the USB connection
between the host machine and the micro-controller. Things to look for:
- Use a good quality USB cable between the host machine and
  micro-controller. Make sure the plugs are secure.
- If using a Raspberry Pi, use a
  [good quality power supply](https://www.raspberrypi.org/documentation/hardware/raspberrypi/power/README.md)
  for the Raspberry Pi and use a
  [good quality USB cable](https://www.raspberrypi.org/forums/viewtopic.php?p=589877#p589877)
  to connect that power supply to the Pi. If you get "under voltage"
  warnings from OctoPrint, this is related to the power supply and it
  must be fixed.
- Make sure the printer's power supply is not being overloaded. (Power
  fluctuations to the micro-controller's USB chip may result in resets
  of that chip.)
- Verify stepper, heater, and other printer wires are not crimped or
  frayed. (Printer movement may place stress on a faulty wire causing
  it to lose contact, briefly short, or generate excessive noise.)
- There have been reports of high USB noise when both the printer's
  power supply and the host's 5V power supply are mixed. (If you find
  that the micro-controller powers on when either the printer's power
  supply is on or the USB cable is plugged in, then it indicates the
  5V power supplies are being mixed.) It may help to configure the
  micro-controller to use power from only one source. (Alternatively,
  if the micro-controller board can not configure its power source,
  one may modify a USB cable so that it does not carry 5V power
  between the host and micro-controller.)

## Raspberry Pi가 인쇄 중에 계속 재부팅됩니다

This is most likely do to voltage fluctuations. Follow the same
troubleshooting steps for a
["Lost communication with MCU"](#i-keep-getting-random-lost-communication-with-mcu-errors)
error.

## "restart_method=command"를 설정하면 AVR 장치가 다시 시작할 때 멈춥니다

Some old versions of the AVR bootloader have a known bug in watchdog
event handling. This typically manifests when the printer.cfg file has
restart_method set to "command". When the bug occurs, the AVR device
will be unresponsive until power is removed and reapplied to the
device (the power or status LEDs may also blink repeatedly until the
power is removed).

The workaround is to use a restart_method other than "command" or to
flash an updated bootloader to the AVR device. Flashing a new
bootloader is a one time step that typically requires an external
programmer - see [Bootloaders](Bootloaders.md) for further details.

## Raspberry Pi가 비정상 종료하면 히터가 켜져 있습니까?

The software has been designed to prevent that. Once the host enables
a heater, the host software needs to confirm that enablement every 5
seconds. If the micro-controller does not receive a confirmation every
5 seconds it goes into a "shutdown" state which is designed to turn
off all heaters and stepper motors.

See the "config_digital_out" command in the
[MCU commands](MCU_Commands.md) document for further details.

In addition, the micro-controller software is configured with a
minimum and maximum temperature range for each heater at startup (see
the min_temp and max_temp parameters in the
[config reference](Config_Reference.md#extruder) for details). If the
micro-controller detects that the temperature is outside of that range
then it will also enter a "shutdown" state.

Separately, the host software also implements code to check that
heaters and temperature sensors are functioning correctly. See the
[config reference](Config_Reference.md#verify_heater) for further
details.

## Marlin 핀 번호를 Klipper 핀 이름으로 어떻게 변환합니까?

Short answer: A mapping is available in the
[sample-aliases.cfg](../config/sample-aliases.cfg) file. Use that file
as a guide to finding the actual micro-controller pin names. (It is
also possible to copy the relevant
[board_pins](Config_Reference.md#board_pins) config section into your
config file and use the aliases in your config, but it is preferable
to translate and use the actual micro-controller pin names.) Note that
the sample-aliases.cfg file uses pin names that start with the prefix
"ar" instead of "D" (eg, Arduino pin `D23` is Klipper alias `ar23`)
and the prefix "analog" instead of "A" (eg, Arduino pin `A14` is
Klipper alias `analog14`).

Long answer: Klipper uses the standard pin names defined by the
micro-controller. On the Atmega chips these hardware pins have names
like `PA4`, `PC7`, or `PD2`.

Long ago, the Arduino project decided to avoid using the standard
hardware names in favor of their own pin names based on incrementing
numbers - these Arduino names generally look like `D23` or `A14`. This
was an unfortunate choice that has lead to a great deal of confusion.
In particular the Arduino pin numbers frequently don't translate to
the same hardware names. For example, `D21` is `PD0` on one common
Arduino board, but is `PC7` on another common Arduino board.

To avoid this confusion, the core Klipper code uses the standard pin
names defined by the micro-controller.

## 내 장치를 특정 유형의 마이크로 컨트롤러 핀에 연결해야 합니까?

It depends on the type of device and type of pin:

ADC pins (or Analog pins): For thermistors and similar "analog"
sensors, the device must be wired to an "analog" or "ADC" capable pin
on the micro-controller. If you configure Klipper to use a pin that is
not analog capable, Klipper will report a "Not a valid ADC pin" error.

PWM pins (or Timer pins): Klipper does not use hardware PWM by default
for any device. So, in general, one may wire heaters, fans, and
similar devices to any general purpose IO pin. However, fans and
output_pin devices may be optionally configured to use `hardware_pwm:
True`, in which case the micro-controller must support hardware PWM on
the pin (otherwise, Klipper will report a "Not a valid PWM pin"
error).

IRQ pins (or Interrupt pins): Klipper does not use hardware interrupts
on IO pins, so it is never necessary to wire a device to one of these
micro-controller pins.

SPI pins: When using hardware SPI it is necessary to wire the pins to
the micro-controller's SPI capable pins. However, most devices can be
configured to use "software SPI", in which case any general purpose IO
pins may be used.

I2C pins: When using I2C it is necessary to wire the pins to the
micro-controller's I2C capable pins.

Other devices may be wired to any general purpose IO pin. For example,
steppers, heaters, fans, Z probes, servos, LEDs, common hd44780/st7920
LCD displays, the Trinamic UART control line may be wired to any
general purpose IO pin.

## M109/M190 "wait for temperature" 요청을 어떻게 취소합니까?

Navigate to the OctoPrint terminal tab and issue an M112 command in
the terminal box. The M112 command will cause Klipper to enter into a
"shutdown" state, and it will cause OctoPrint to disconnect from
Klipper. Navigate to the OctoPrint connection area and click on
"Connect" to cause OctoPrint to reconnect. Navigate back to the
terminal tab and issue a FIRMWARE_RESTART command to clear the Klipper
error state.  After completing this sequence, the previous heating
request will be canceled and a new print may be started.

## 프린터 탈조를 어떻게 알 수 있습니까?

In a way, yes. Home the printer, issue a `GET_POSITION` command, run
your print, home again and issue another `GET_POSITION`. Then compare
the values in the `mcu:` line.

This might be helpful to tune settings like stepper motor currents,
accelerations and speeds without needing to actually print something
and waste filament: just run some high-speed moves in between the
`GET_POSITION` commands.

Note that endstop switches themselves tend to trigger at slightly
different positions, so a difference of a couple of microsteps is
likely the result of endstop inaccuracies. A stepper motor itself can
only lose steps in increments of 4 full steps. (So, if one is using 16
microsteps, then a lost step on the stepper would result in the "mcu:"
step counter being off by a multiple of 64 microsteps.)

## Klipper가 "I lost my print" 오류를 보고하는 이유는 무엇입니까?

Short answer: We want to know if our printers detect a problem so that
the underlying issue can be fixed and we can obtain great quality
prints. We definitely do not want our printers to silently produce low
quality prints.

Long answer: Klipper has been engineered to automatically workaround
many transient problems. For example, it automatically detects
communication errors and will retransmit; it schedules actions in
advance and buffers commands at multiple layers to enable precise
timing even with intermittent interference. However, should the
software detect an error that it can not recover from, if it is
commanded to take an invalid action, or if it detects it is hopelessly
unable to perform its commanded task, then Klipper will report an
error. In these situations there is a high risk of producing a
low-quality print (or worse). It is hoped that alerting the user will
empower them to fix the underlying issue and improve the overall
quality of their prints.

There are some related questions: Why doesn't Klipper pause the print
instead? Report a warning instead? Check for errors before the print?
Ignore errors in user typed commands? etc? Currently Klipper reads
commands using the G-Code protocol, and unfortunately the G-Code
command protocol is not flexible enough to make these alternatives
practical today. There is developer interest in improving the user
experience during abnormal events, but it is expected that will
require notable infrastructure work (including a shift away from
G-Code).

## 최신 소프트웨어로 업그레이드하려면 어떻게 합니까?

The first step to upgrading the software is to review the latest
[config changes](Config_Changes.md) document. On occasion, changes are
made to the software that require users to update their settings as
part of a software upgrade. It is a good idea to review this document
prior to upgrading.

When ready to upgrade, the general method is to ssh into the Raspberry
Pi and run:

```
cd ~/klipper
git pull
~/klipper/scripts/install-octopi.sh
```

Then one can recompile and flash the micro-controller code. For
example:

```
make menuconfig
make clean
make

sudo service klipper stop
make flash FLASH_DEVICE=/dev/ttyACM0
sudo service klipper start
```

However, it's often the case that only the host software changes. In
this case, one can update and restart just the host software with:

```
cd ~/klipper
git pull
sudo service klipper restart
```

If after using this shortcut the software warns about needing to
reflash the micro-controller or some other unusual error occurs, then
follow the full upgrade steps outlined above.

If any errors persist then double check the
[config changes](Config_Changes.md) document, as you may need to
modify the printer configuration.

Note that the RESTART and FIRMWARE_RESTART g-code commands do not load
new software - the above "sudo service klipper restart" and "make
flash" commands are needed for a software change to take effect.

## Klipper를 어떻게 제거합니까?

On the firmware end, nothing special needs to happen. Just follow the
flashing directions for the new firmware.

On the raspberry pi end, an uninstall script is available in
[scripts/klipper-uninstall.sh](../scripts/klipper-uninstall.sh). For
example:
```
sudo ~/klipper/scripts/klipper-uninstall.sh
rm -rf ~/klippy-env ~/klipper
```
