# 자주 묻는 질문들

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

USB 직렬 포트를 찾는 일반적인 방법은 호스트 시스템의 ssh 터미널에서 다음의 명령을 실행하면 됩니다. `ls /dev/serial/by-id/*`. 그럼 다음과 같이 나옵니다:
```
/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
```

위 명령에서 찾은 이름을 사용해서 config 파일에 적을 수도 있고 마이크로 컨트롤러에 펌웨어 업로드 할때도 사용됩니다. 예를 들어 flash 명령은 다음과 같이 사용할 수 있습니다.
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

권장 하드웨어는 Raspberry Pi 2, Raspberry Pi 3 또는 Raspberry Pi 4입니다.

Klipper는 Raspberry Pi 1 및 Raspberry Pi Zero에서 실행되지만 이러한 보드에는 OctoPrint를 제대로 실행하기에 충분한 처리 능력이 없습니다. OctoPrint에서 직접 인쇄할 때 이러한 느린 시스템에서 인쇄 중단이 발생하는 것은 일반적입니다. (프린터는 OctoPrint가 이동 명령을 보낼 수 있는 것보다 더 빠르게 움직일 수 있습니다.) 어쨌든 이러한 느린 보드 중 하나에서 실행하려면 인쇄할 때 "virtual_sdcard" 기능을 사용하는 것을 고려하십시오(자세한 내용은 [config reference](Config_Reference.md#virtual_sdcard) 참조).

Beaglebone에서 실행하려면 [Beaglebone specific installation instructions](beaglebone.md) 을 참조하십시오.

Klipper는 다양한 컴퓨터에서 실행될 수 있습니다. Klipper 호스트 소프트웨어에는 Linux(또는 이와 유사한) 컴퓨터에서 실행되는 Python만 있으면 됩니다. 그러나 다른 시스템에서 실행하려면 기본적으로 필요한 소프트웨어를 설치하기 위해 Linux 관리자 지식이 필요합니다. 필요한 Linux 관리자에 대한 추가 정보는 [install-octopi.sh](../scripts/install-octopi.sh) 를 참조하십시오.

저가형 칩에서 Klipper 호스트 소프트웨어를 실행하려는 경우 최소한 "double precision floating point" 하드웨어가 있는 시스템이 필요하다는 점에 유의하십시오.

만약 일반 컴퓨터 또는 서버 시스템에서 Klipper 호스트 소프트웨어를 실행하려는 경우 Klipper에는 몇 가지 'real-time scheduling'에 대한 요구 사항이 있습니다. 인쇄 중에 호스트 컴퓨터가 계산에 부하가 걸리는 컴퓨팅 작업(예: 하드 드라이브 조각 모음, 3d 렌더링, 대량 스와핑 등)을 수행하는 경우 Klipper가 출력 중 오류를 낼 수 있습니다.

참고: OctoPi 이미지를 사용하지 않는 경우 여러 Linux 배포판에서 직렬 통신을 방해할 수 있는 "ModemManager"(또는 유사한) 패키지를 활성화한다는 점에 유의하십시오. (이로 인해 Klipper가 무작위로 보이는 "MCU와의 통신 끊김" 오류를 보고할 수 있습니다.) 이러한 배포판 중 하나에 Klipper를 설치하는 경우 해당 패키지를 비활성화해야 할 수 있습니다.

## 동일한 호스트 시스템에서 여러 Klipper 인스턴스를 실행할 수 있습니까?

Klipper 호스트 소프트웨어의 여러 인스턴스를 실행할 수 있지만 그렇게 하려면 Linux 관리자 지식이 필요합니다. Klipper 설치 스크립트는 궁극적으로 다음 Unix 명령이 실행되도록 합니다:
```
~/klippy-env/bin/python ~/klipper/klippy/klippy.py ~/printer.cfg -l /tmp/klippy.log
```
각 인스턴스에 자체 프린터 구성 파일, 자체 로그 파일 및 전용 tty가 있는 한 위 명령의 여러 인스턴스를 실행할 수 있습니다. 예를 들어:
```
~/klippy-env/bin/python ~/klipper/klippy/klippy.py ~/printer2.cfg -l /tmp/klippy2.log -I /tmp/printer2
```

만약 이런 방식으로 여러 개의 Klipper 를 사용하기를 선택하였다면 시작, 중지 및 설치 스크립트(있는 경우)를 구현해야 합니다.[install-octopi.sh](../scripts/install-octopi.sh) 스크립트와 [klipper-start.sh](../scripts/klipper-start.sh) 스크립트가 예제가 유용할 수 있습니다.

## OctoPrint를 사용해야 합니까?

Klipper 소프트웨어는 OctoPrint에 의존하지 않습니다. 대체 소프트웨어를 사용하여 Klipper에 명령을 보낼 수 있지만 그렇게 하려면 Linux 관리자 지식이 필요합니다.

Klipper는 "/tmp/printer" 파일을 통해 "가상 직렬 포트"를 생성하고 해당 파일을 통해 고전적인 3d 프린터 직렬 인터페이스를 에뮬레이션 합니다. 일반적으로 프린터 직렬 포트에 "/tmp/printer"를 사용하도록 구성할 수 있는 한 대체 소프트웨어가 Klipper와 함께 작동할 수 있습니다.

## 프린터를 원점으로 이동하기 전에 스테퍼를 이동할 수 없는 이유는 무엇입니까?

이 기능은 실수로 익스트루더를 BED 나 벽에 부딪힐 가능성을 줄이기 위함입니다. 프린터가 홈으로 이동하면 소프트웨어는 각 이동이 구성 파일에 정의된 position_min/max 내에 있는지 확인하려고 시도합니다. 모터가 비활성화된 경우(M84 또는 M18 명령을 통해)도 모터는 이동하기 전에 다시 원점 복귀해야 합니다.

OctoPrint를 통해 인쇄를 취소한 후 헤드를 이동하려면 OctoPrint 취소 순서를 변경하는 것이 좋습니다. 설정->GCODE 스크립트 아래의 웹 브라우저를 통해 OctoPrint에서 구성됩니다.

인쇄가 완료된 후 헤드를 이동하려면 슬라이서의 "custom gcode" 섹션에 원하는 이동을 추가하는 것이 좋습니다.

만일 프린터가 오토홈을 할때 추가적인 작업이 필요한 경우 (또는 근본적으로 오토홈 프로세스가 없는 경우) config 파일에서 safe_z_home 또는 homing_override 섹션 사용을 고려하십시오. 진단 또는 디버깅 목적으로 스테퍼를 이동해야 하는 경우 config 파일에 force_move 섹션을 추가하는 것을 고려하십시오. 이러한 옵션에 대한 자세한 내용은 [config reference](Config_Reference.md#customized_homing)을 참조를 참조하세요.

## 기본 설정에서 Z position_endstop이 0.5로 설정된 이유는 무엇입니까

직교 스타일 프린터의 경우 Z position_endstop은 엔드스톱이 트리거될 때 노즐이 BED에서 얼마나 멀리 떨어져 있는지 지정합니다. 가능하면 Z-max 엔드스톱을 사용하고 BED에서 멀리 떨어져 있는 것이 좋습니다 (이렇게 하면 BED에 충돌 가능성이 줄어듭니다). 그러나 BED를 향해 오토홈 해야 하는 경우 노즐이 BED에서 여전히 가까운 거리에 있을 때 트리거되도록 엔드스톱을 배치하는 것이 좋습니다. 이렇게 하면 축을 원점 복귀시킬 때 노즐이 BED에 닿기 전에 멈춥니다. 자세한 [bed level document](Bed_Level.md) 를 참조하십시오.

## Marlin에서 설정을 가져왔는데 X/Y 축은 잘 작동하지만 Z 축을 원점 복귀할 때 삐걱거리는 소리가 납니다

짧은 대답: 먼저 [config check document](Config_checks.md)에 설명된 대로 스테퍼 구성을 확인했는지 확인합니다. 문제가 지속되면 프린터 구성에서 max_z_velocity 설정을 줄여 보십시오.

긴 대답: 실제로 Marlin 은 일반적으로 초당 약 10000 step 의 속도로만 움직일 수 있습니다. 더 높은 속도로 이동하도록 요청되면 Marlin은 일반적으로 최대한 가능한 정도만 수행합니다. Klipper는 훨씬 더 높은 속도를 달성할 수 있지만 스테퍼 모터는 더 높은 속도로 이동하기에 충분한 토크가 없을 수 있습니다. 따라서 높은 기어비 또는 높은 마이크로스텝이 있는 Z축의 경우 실제 얻을 수 있는 max_z_velocity를 설정하는 것이 Marlin에서 구성된 것보다 작을 수 있습니다.

## TMC 모터 드라이버가 인쇄 도중에 꺼집니다

"standalone mode"에서 TMC2208(또는 TMC2224) 드라이버를 사용하는 경우 최신 버전의 Klipper를 사용해야 합니다. TMC2208 "stealthchop" 드라이버 문제에 대한 해결 방법이 2020년 3월 중순에 Klipper에 추가되었습니다.

## 랜덤하게 "MCU와의 통신 끊김" 오류가 계속 발생합니다

이것은 일반적으로 호스트 시스템과 마이크로 컨트롤러 간의 USB 연결에 대한 하드웨어 오류로 인해 발생합니다. 아래 사항을 점검하세요:
- 호스트 머신과 마이크로 컨트롤러 사이에 좋은 품질의 USB 케이블을 사용하십시오. 플러그가 안전한지 확인하십시오.  
- Raspberry Pi를 사용하는 경우 Raspberry Pi에 [good quality power supply](https://www.raspberrypi.org/documentation/hardware/raspberrypi/power/README.md)를 사용하고 [good quality USB cable](https://www.raspberrypi.org/forums/viewtopic.php?p=589877#p589877)을 사용하여 해당 전원 공급 장치를 Pi에 연결합니다. OctoPrint에서 "전압 부족" 경고가 표시되면 이는 전원 공급 장치와 관련된 것이며 수정해야 합니다.  
- 프린터의 전원 공급 장치에 과부하가 걸리지 않았는지 확인하십시오. (마이크로 컨트롤러의 USB 칩에 대한 전력 변동으로 인해 해당 칩이 재설정될 수 있습니다.)
- 스테퍼 모터, 히터 및 기타 프린터 와이어가 구겨지거나 닳지 않았는지 확인합니다. (프린터가 움직이면 결함이 있는 전선에 스트레스가 가해져 접촉이 끊어지거나 잠시 단락되거나 과도한 소음이 발생할 수 있습니다.)  
- 프린터의 전원 공급 장치와 호스트의 5V 전원 공급 장치가 혼합되어 있을 때 USB 노이즈가 높다는 보고가 있었습니다. (프린터의 전원 공급 장치가 켜져 있거나 USB 케이블이 꽂혀 있을 때 마이크로 컨트롤러의 전원이 켜지면 5V 전원 공급 장치가 혼합되고 있음을 나타냅니다.) 단 하나의 소스에서 전력을 공급하는 것이 좋습니다. (또는 마이크로 컨트롤러 보드가 전원을 구성할 수 없는 경우 호스트와 마이크로 컨트롤러 간에 5V 전원을 전달하지 않도록 USB 케이블을 수정할 수 있습니다.)

## Raspberry Pi가 인쇄 중에 계속 재부팅됩니다

이것은 대부분 전압 변동이 원인일 가능성이 있습니다. 문제를 해결하려면 ["MCU와의 통신 끊김"](#랜덤하게-MCU와의-통신-끊김-오류가-계속-발생합니다)를 따르십시오.

## "restart_method=command"를 설정하면 AVR 장치가 다시 시작할 때 멈춥니다

AVR 부트로더의 일부 이전 버전에는 워치독 이벤트 처리에 알려진 버그가 있습니다. 이것은 일반적으로 printer.cfg 파일에 restart_method가 "command"로 설정되어 있을 때 나타납니다. 버그가 발생하면 전원이 제거되고 장치에 다시 공급될 때까지 AVR 장치가 응답하지 않습니다 (전원이 제거될 때까지 전원 또는 상태 LED도 반복적으로 깜박일 수 있음).

해결 방법은 "command" 이외의 restart_method를 사용하거나 업데이트된 부트로더를 AVR 장치에 플래시하는 것입니다. 새 부트로더를 플래싱하는 것은 일반적으로 외부 프로그래머가 필요한 일회성 단계입니다. 자세한 내용은 [Bootloaders](Bootloaders.md)를 참조하십시오.

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

소프트웨어 업그레이드의 첫 번째 단계는 최신 [config changes](Config_Changes.md) 를 검토하는 것입니다. 업그레이드하기 전에 이 문서를 검토하는 것이 좋습니다.

업그레이드할 준비가 되면 일반적인 방법은 Raspberry Pi에 ssh 로 접속하고 다음을 실행하는 것입니다:

```
cd ~/klipper
git pull
~/klipper/scripts/install-octopi.sh
```

그런 다음 마이크로 컨트롤러 코드를 다시 컴파일하고 펌웨어 업로드 할 수 있습니다. 예를 들어:

```
make menuconfig
make clean
make

sudo service klipper stop
make flash FLASH_DEVICE=/dev/ttyACM0
sudo service klipper start
```

그러나 호스트 소프트웨어만 변경되는 경우가 많습니다. 이 경우 다음을 사용하여 호스트 소프트웨어만 업데이트하고 다시 시작할 수 있습니다:

```
cd ~/klipper
git pull
sudo service klipper restart
```

만약에 이렇게 했는데도 마이크로 컨트롤러를 다시 펌업 해야 한다고 경고하거나 기타 비정상적인 오류가 발생하면 위에 설명된 전체 업그레이드 단계를 따르십시오.

오류가 지속되면 프린터 config 를 수정해야 할 수 있으므로 [config changes](Config_Changes.md)를 다시 확인하십시오.

RESTART 및 FIRMWARE_RESTART gcode 명령은 새 호스트 소프트웨어를 로드하지 않습니다. 호스트 소프트웨어 변경 사항을 적용하려면 위의 "sudo service klipper restart" 및 "make flash" 명령이 필요합니다

## Klipper를 어떻게 제거합니까?

펌웨어 쪽에서는 특별히 해 줄 일이 없습니다. 그냥 새로 사용할 펌웨어 업로드 가이드를 따라서 업로드 하세요.

라즈베리 파이 쪽에서 제거 스크립트는 [scripts/klipper-uninstall.sh](../scripts/klipper-uninstall.sh) 를 사용할 수 있습니다. 예를 들어:
```
sudo ~/klipper/scripts/klipper-uninstall.sh
rm -rf ~/klippy-env ~/klipper
```
