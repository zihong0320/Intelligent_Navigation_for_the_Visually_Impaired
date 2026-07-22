# 시각장애인을_위한_지능형_도보_내비게이션

## 0. Summary
### 🛠 개발 환경 및 Architecture
* **Target Board**: Basys3 (Xilinx Artix-7)
* **Camera Module**: OV7670
* **Development Tools**: Vivado, VS Code
* **Languages**: SystemVerilog, Verilog HDL, Python

---

### 👥 Team Members & Task Allocation

| Name | Role | Key Responsibilities |
| :--- | :--- | :--- |
| **김지홍** | **BLUETOOTH** | FPGA $\to$ PC 패킷 전송, FIFO 기반 Latency 및 Pop 타이밍 제어, 음악 ROM 파일 생성 |
| **윤수현** | **OLLAMA** | OV7670 레지스터 설정, VGA Timing Generator 및 픽셀 출력 |
| **조준호** | **USB_Speaker** | 게임 메인 상태 머신(FSM) 설계, 모듈간 동기화 제어 |
| **최은수** | **YOLO11_training** | 프레임 단위 노트 Y축 위치 이동 관리 (최대 16개 노트) |

---
