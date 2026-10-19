# Mini Radar Scanner

서보 모터에 거리 센서를 달아 주변을 스캔하고, 라즈베리 파이에서 레이다 화면을 그리며 움직이는 물체를 칼만 필터로 추적하는 임베디드 프로젝트입니다.

- **STM32 (NUCLEO-F446RE)**: RTOS 태스크 분리, DMA 기반 측정, 스캔 제어
- **Raspberry Pi 4**: 커널 드라이버로 데이터 수신, 사용자 공간에서 추적·시각화·로그

동작하는 데모보다 **설계 근거와 실측 수치**를 남기는 것을 목표로 합니다.

## 진행 상황

| 단계 | 내용 | 상태 |
|---|---|---|
| 00 | Hello World: LD2 토글 + USART2 printf | ✅ 완료 |
| 01 | 버튼 인터럽트 (EXTI, NVIC) | ⬜ |
| 02 | 타이머 (실행 시간 측정, 서보 PWM) | ⬜ |
| 03 | RTOS 도입 | ⬜ |
| 04 | UART DMA, 로그 태스크 | ⬜ |
| 05 | 센서·서보 스캔, 스캔 주기·지연 실측 | ⬜ |
| 06 | 라즈베리 파이 커널 드라이버 | ⬜ |
| 07 | 칼만 필터 추적, 레이다 화면, 성능 평가 | ⬜ |

## 저장소 구조

```
.
├── hello_f446/        # 00: Hello World (STM32CubeIDE 프로젝트)
├── .gitignore
├── .gitattributes
└── LICENSE
```

## 개발 환경

실측 수치는 아래 환경에서 측정한 값입니다. 툴 버전이 바뀌면 코드 크기와 실행 시간이 달라질 수 있습니다.

### 하드웨어
| 항목 | 내용 |
|---|---|
| MCU 보드 | NUCLEO-F446RE (MB1136 rev C), STM32F446RET6, Cortex-M4F |
| 디버거 | 보드 내장 ST-LINK/V2-1, 펌웨어 V2J48M35 |
| 클럭 설정 | HSI 16 MHz → PLL → SYSCLK/HCLK 84 MHz (CubeMX 보드 기본값) |
| 상위 처리 보드 | Raspberry Pi 4 Model B (예정) |

### 소프트웨어
| 항목 | 버전 |
|---|---|
| 호스트 OS | Windows (x86_64) |
| STM32CubeIDE | 2.2.0 (build 29186) |
| STM32CubeMX | 6.18.1 |
| STM32CubeF4 펌웨어 패키지 (HAL) | V1.28.3 |
| GCC 툴체인 | GNU Tools for STM32 14.3.rel1 |
| GDB | arm-none-eabi-gdb 15.2.90.20241229 |

## 빌드와 실행 (hello_f446)

1. STM32CubeIDE에서 **File → Import → General → Existing Projects into Workspace**를 열고 `hello_f446` 폴더를 선택합니다.
   "Copy projects into workspace"는 체크하지 않습니다.
2. **Build** (`Ctrl+B`)
3. 보드를 ST-LINK 쪽 USB(Mini-B)로 연결하고 **Run** (`Ctrl+F11`)으로 플래싱합니다.
4. 시리얼 터미널을 ST-LINK Virtual COM Port에 **115200 8N1**로 연결하고, 보드의 RESET(B2)을 누릅니다.

출력 예시:
```
Hello, NUCLEO-F446RE! SYSCLK = 84000000 Hz
tick 0, uptime_ms = 4
tick 1, uptime_ms = 507
```

## 측정 기록

| 단계 | Flash | RAM | 비고 |
|---|---|---|---|
| 00 hello_f446 | 14,000 B (2.7%) | 2,080 B (1.6%) | Debug 구성, `arm-none-eabi-size` 기준 |

## 라이선스

- 이 저장소에서 직접 작성한 코드는 [MIT License](LICENSE)를 따릅니다.
- `*/Drivers/` 아래의 STMicroelectronics HAL과 Arm CMSIS는 각 폴더의 `LICENSE.txt`에 적힌 원래 라이선스를 따릅니다.
- 라즈베리 파이 커널 모듈(예정)은 리눅스 커널 요구사항에 따라 GPL-2.0으로 배포합니다.
