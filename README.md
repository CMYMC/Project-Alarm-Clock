# Bluetooth Alarm Clock

## 1. 개요

**STM32F429ZI Nucleo** 보드를 기반으로 제작한  알람 시계입니다. 일상생활에서 실제로 활용 가능한 시계를 목표로 개발하였으며, 기본적인 시간 및 알람 설정 기능 외에도 사용자가 원하는 알람 음악을 선택 및 제어할 수 있는 기능을 구현했습니다.



## 2. 구성도 (System Architecture)



* **Main Controller (STM32):** 시간 계수(RTC), 상태 관리, 주변 장치 제어
* **Input/Output (Shield):** LCM1602 IIC Shield를 이용한 시간 정보 출력 및 버튼 입력 처리
* **Actuator (Buzzer):** Timer PWM을 활용한 알람 멜로디 출력

## 3. 기능 설명

### 모드별 동작 (Mode Operation)


사용자 버튼(User Button)을 눌러 4가지 모드를 순환하며 기능을 설정합니다.

1.  **Normal Mode (기본 시계):** 현재 시간(AM/PM, 시:분:초)과 알람 설정 여부(AL 아이콘)를 표시합니다.
2.  **Time Setting Mode (시간 설정):**
    * Shield의 상하좌우 버튼을 이용해 AM/PM 및 시, 분, 초를 변경합니다.
    * 커서를 이동하여 원하는 단위의 값을 직관적으로 수정할 수 있습니다.
3.  **Alarm Setting Mode (알람 설정):**
    * 시간 설정과 동일한 방식으로 알람 시간을 지정합니다.
    * 설정이 완료되면 화면에 'AL' 심볼이 표시되며, 해당 시간이 되면 부저가 울립니다.
4.  **Music Select Mode (음악 선택):**
    * 3가지 알람음(오징어 게임, 산토끼) 중 하나를 선택할 수 있습니다.
    * 선택된 음악은 알람 작동 시 PWM 신호를 통해 부저로 연주됩니다.


## 4. 시연 영상 (Demonstration)



## 5. 주요 기술

| 구성 요소 | 역할 및 기술 |
| :--- | :--- |
| **STM32F429ZI** | 메인 MCU, 전반적인 시스템 로직 및 상태 머신(State Machine) 구현 |
| **STM32 CubeIDE** |  펌웨어 개발 환경 |
| **Peripherals** | **Timer/PWM** (부저 멜로디 재생), **ADC** (버튼 입력), **RTC** (시간 유지), **I2C** (LCD 제어) |
| **Bluetooth (UART)** | 모바일 앱과 MCU 간의 비동기 시리얼 통신 구현 |
| **Algorithm** | 4가지 모드 간 전환을 위한 상태 제어 및 버튼 디바운싱 알고리즘 |

## 6. 고찰 (Review)

