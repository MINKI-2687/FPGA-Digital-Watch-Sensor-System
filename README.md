# FPGA 기반 통합 디지털 시계 및 다중 센서 제어 시스템

FPGA(Basys3) 위에서 **디지털 시계/스톱워치**, **UART 통신**, **FIFO 버퍼**, **HC-SR04 초음파 센서**, **DHT11 온습도 센서**를 통합한 디지털 시스템입니다.  
모든 모듈을 Verilog로 설계하고, FSM 기반의 제어 유닛으로 전체 시스템을 관리합니다.

## 🏗️ 시스템 구조
```
                    ┌─────────────────────────────────────┐
                    │           system_top                 │
                    │                                     │
  Button ──────────>│  ┌──────────┐   ┌───────────────┐  │
  (Debounced)       │  │ Control  │──>│  Stopwatch /   │  │
                    │  │  Unit    │   │  Watch FSM     │  │──── FND Display
                    │  └──────────┘   └───────────────┘  │
                    │                                     │
  HC-SR04 ─────────>│  ┌──────────┐   ┌──────┐          │
  (Ultrasonic)      │  │ SR04     │──>│ FIFO │──> UART ──│──── PC (Serial)
                    │  │Controller│   │      │    TX/RX  │
  DHT11 ───────────>│  │ DHT11   │──>│      │          │
  (Temp/Humidity)   │  │Controller│   └──────┘          │
                    └─────────────────────────────────────┘
```

## 📁 디렉토리 구조
```
.
├── rtl/
│   ├── system_top.v              # 최상위 시스템 통합 모듈
│   ├── top_stopwatch_watch.v     # 시계/스톱워치 FSM Top
│   ├── control_unit.v            # FSM 기반 제어 유닛
│   ├── uart_top.v                # UART TX/RX 통합 모듈
│   ├── fifo.v                    # FIFO 버퍼 (비동기 데이터 전달)
│   ├── ascii_decoder.v           # ASCII 디코더 (센서 데이터 → 문자 변환)
│   ├── controller_SR04.v         # HC-SR04 초음파 센서 제어
│   ├── dht11_controller.v        # DHT11 온습도 센서 제어
│   ├── fnd_controller.v          # 7-Segment FND 출력 제어
│   ├── btn_debounce.v            # 버튼 디바운싱
│   ├── btn_debounce_top.v        # 버튼 디바운싱 Top
│   └── tick_trigger.v            # 타이밍 틱 생성기
└── README.md
```

## 🔧 기술 스택
| 항목 | 내용 |
|------|------|
| 설계 언어 | Verilog |
| 시뮬레이션 | Xilinx Vivado Simulator |
| 타겟 FPGA | Basys3 (Xilinx Artix-7) |
| 센서 | HC-SR04 (초음파), DHT11 (온습도) |
| 통신 | UART (115200 baud) |

## ⚙️ 주요 모듈 설명

### 🕐 시계 / 스톱워치
- **Moore FSM** 기반의 시계 및 스톱워치 모드 전환
- 버튼 입력을 통한 모드 변경, 시간 설정, 스톱워치 시작/정지/리셋
- 7-Segment FND 디스플레이에 시간 출력

### 📡 UART 통신
- 115200 baud rate, 8N1 포맷
- FIFO 버퍼를 통한 데이터 축적 및 순차 전송
- PC에서 시리얼 모니터로 센서 데이터 실시간 확인

### 🌡️ 센서 제어
- **HC-SR04**: Trigger 펄스 → Echo 응답 시간 측정 → 거리(cm) 계산
- **DHT11**: 1-Wire 프로토콜로 온도/습도 데이터 수신 및 파싱