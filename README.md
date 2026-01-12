#  Alarm Clock

## 1. 개요

**STM32 MCU의 내부 자원(Flash, Timer, ADC)을 최대로 활용한 디지털 알람 시계입니다. 단순한 시간 표시를 넘어, 상태 머신을 통한 체계적인 모드 관리와 Flash 메모리 제어를 통해 데이터 보존성을 구현
STM32F429ZI Nucleo** 보드를 기반으로 제작한  알람 시계입니다. 일상생활에서 실제로 활용 가능한 시계를 목표로 개발하였으며, 기본적인 시간 및 알람 설정 기능 외에도 사용자가 원하는 알람 음악을 선택 및 제어할 수 있는 기능을 구현했습니다.
본 프로젝트는 STM32 입문 과정에서 학습한 Timer, ADC, 인터럽트, 통신(I2C/UART)등의 개별 기술을 하나의 시스템으로 통합 구현해보고자 STM32 알람시계 프로젝트를 기획



## 2. 시스템 아키텍쳐
### 1. 시스템 구성도 
![구성도](https://github.com/user-attachments/assets/34aae5f9-291a-4256-8f5b-97dbb59b5186)



### 2. Software FSM

## 3. 기능 설명
### 1. 상태 머신 기반 모드 전환 
사용자 버튼(User Button)을 눌러 4가지 모드를 순환하며 기능을 설정합니다.
1. **Normal Mode (기본 시계):** 현재 시간(AM/PM, 시:분:초)과 알람 설정 여부(AL)를 표시
2. **Time Setting Mode (시간 설정)**
    *  Shield의 상하좌우 버튼을 이용해 AM/PM 및 시, 분, 초를 변경
   * 커서를 이동하여 원하는 단위의 값을 설정 및 음악 선택
3.  **Alarm Setting Mode (알람 설정):**
    * 시간 설정과 동일한 방식으로 알람 시간을 지정
    * 설정이 완료되면 화면에 'AL' 심볼이 표시되며, 해당 시간이 되면 부저가 울림
4.  **Music Select Mode (음악 선택):**
    * 2가지 알람음(오징어 게임, ) 중 하나를 선택
    * 선택된 음악은 알람 작동 시 PWM 신호를 통해 부저로 연주

### 2. ADC 버튼 제어
5개의 버튼을 단일 ADC핀으로 식별하고, 펄스 폭 측정을 통해 입력 형태를 구분
1. short Click: 모드 변경 및 설정값 커서 이동
2. Long Click (3초 이상) : 음악 선택 모드 진입
3. Double Click : 알람 설정 모드로 진입
    *  Shield의 상하좌우 버튼을 이용해 AM/PM 및 시, 분, 초를 변경
   * 커서를 이동하여 원하는 단위의 값을 설정 및 음악 선택

### 3. 멜로디 알람 
- 타이머 인터럽트를 통해 설정된 시간에 정확히 알람을 발생시키고, PWM 주파수 변조를 통해 멜로디 재생

### 4. 메모리 저장
- MCU 내부 Flash 메모리 섹터를 제어
 - 전원이 차단되어도 사용자가 설정한 시간, 알람 시각, 멜로디 정보가 지워지지 않음




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





### 3) Internal Flash 데이터 보존
- STM32 내부 Flash의 **Sector 23** 영역을 비휘발성 저장소로 활용했습니다.
- 전원이 차단되어도 사용자가 설정한 시간, 알람 시각, 멜로디 정보가 지워지지 않습니다.

### 4) PWM 멜로디 알람
- Timer 인터럽트로 정확한 시간에 알람 이벤트를 발생시킵니다.
- PWM 주파수 변조를 통해 단순 비프음이 아닌 '오징어게임', '산토끼' 등의 멜로디를 재생합니다.

---

## 4. 🛠️ Tech Stack
| Category | Technology |
| :--- | :--- |
| **MCU** | STM32F429ZI (Nucleo-144) |
| **Language** | C (HAL Library) |
| **IDE** | STM32CubeIDE |
| **H/W Components** | I2C LCD (16x2), Passive Buzzer, User Button, 5-Tact Switch Shield |
| **Key Tech** | FSM, ADC(DMA/Interrupt), Internal Flash, Timer PWM |

---

## 5. ⚡ Troubleshooting


| Issue | Cause | Solution |
| :--- | :--- | :--- |
| **LCD 화면 프리징** | Flash 쓰기 함수(Blocking)를 타이머 인터럽트 내에서 호출하여 시스템 자원 점유 | Flash 저장 로직을 ISR에서 분리하고, 버튼 이벤트 시 1회 실행되는 외부 인터럽트 콜백으로 이관 |
| **버튼 입력 오동작** | 단순 Polling 방식은 버튼의 바운싱과 애매한 누름 속도를 구분하지 못함 | Rising/Falling 엣지 인터럽트를 모두 사용하여 펄스 폭(Time Interval)을 정밀 측정하는 알고리즘 구현 |

--
