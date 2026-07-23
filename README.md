# 🦯 Intelligent Walking Navigation for the Visually Impaired

> **시각장애인을 위한 딥러닝 기반 실시간 온디바이스 보행 보조 안내 시스템**  
> Jetson Orin Nano 기반 다중 YOLO 객체 탐지, 로컬 LLM(Ollama) 상황 인지 및 실시간 TTS 음성 안내 시스템

---

## 📌 0. Summary

### 🎯 Overview
- **실시간 다중 객체 탐지**: 다중 YOLO 모델을 활용해 보행 중 만나는 다양한 장애물(사람, 차량, 신호등, 횡단보도, 계단, 맨홀 등)을 고신뢰도로 실시간 분류 및 위치 추적
- **상황 맞춤형 문장 생성**: 탐지된 객체의 위치/거리 정보를 종합하여 로컬 LLM(Ollama) 기반으로 보행자 중심의 직관적인 안내 문장 생성
- **실시간 음성 출력**: 생성된 안내 문장을 gTTS 및 유무선(USB/Bluetooth) 스피커를 통해 실시간 음성(Voice)으로 출력

---

### 🛠 개발 환경 및 Architecture
* **Target Board**: NVIDIA Jetson Orin Nano Developer Kit
* **Camera**: Logitech C270 HD Webcam
* **Audio**: Bluetooth Speaker, USB Speaker
* **AI Models**: Ultralytics YOLO11 (TensorRT `.engine` 변환), Ollama (Local LLM), gTTS
* **Languages & Tools**: Python, VS Code

---

### 👥 담당 역할
- **코드 총괄**: 메인 파이프라인 및 데이터 플로우 통합 설계
- **Ollama 적용**: Jetson 보드 내 로컬 LLM 구축 및 Python API 연동, 프롬프트 엔지니어링
- **다중 객체 탐지 & 음성 출력**: 다중 YOLO 추론 결과 파싱 및 Bluetooth/USB 스피커 음성 출력 연동

---

### 🎯 구현 목표
- YOLO 모델을 활용한 실시간 다중 객체 분류 및 위치 탐지
- Jetson 보드 내 Ollama(로컬 LLM) 구축 및 파이썬 연동을 통한 자연스러운 상황 안내 문장 생성
- USB 및 Bluetooth 스피커를 통한 저지연 오디오 안내 출력 지원

---

## 📚 1. Introduction

### 1.1 YOLO (You Only Look Once)
이미지 전체를 한 번에 확인하여 객체의 위치(Bounding Box)와 클래스(Class)를 동시에 추론하는 고속 실시간 객체 탐지 인공지능 모델입니다. 본 프로젝트에서는 TensorRT 엔진 변환을 통해 온디바이스 환경에서 초고속 추론을 수행합니다.

### 1.2 Ollama
대규모 언어 모델(LLM)을 외부 서버 통신 없이 로컬 기기에서 독립적으로 구동할 수 있게 해주는 오픈소스 인프라 플랫폼입니다. 인터넷 연결이 불안정한 보행 환경에서도 온디바이스로 안전하게 자연어 처리를 수행합니다.

---

## ⚙️ 2. Specification

### 2.1 Hardware Specification

| Component | Model / Spec | Description |
| :--- | :--- | :--- |
| **Main Board** | **NVIDIA Jetson Orin Nano** | NVIDIA Ampere GPU 기반, 온디바이스 다중 YOLO 추론 및 로컬 LLM(Ollama) 연산 핵심 장치 |
| **Camera** | **Logitech C270 HD Webcam** | HD 720p 지원 (프로젝트 연동 해상도: `640x480`), 실시간 보행 영상 프레임 공급 |
| **Audio** | **Bluetooth / USB Speaker** | 시각장애인 대상 실시간 상황 안내 음성 출력 |

---

### 2.2 Software Specification

| Category | Component / Spec | Description |
| :--- | :--- | :--- |
| **Language** | Python | 전체 파이프라인 제어 및 모듈 연동 |
| **AI Framework** | Ultralytics YOLO11 | TensorRT (`.engine`) 포맷 변환을 통한 추론 가속 |
| **YOLO Models** | Multi-Object Detection | • `coco_model` (`imgsz=320`)<br>• `custom_model_1` (`imgsz=640`)<br>• `custom_model_2` (`imgsz=640`)<br>• `custom_model_3` (`imgsz=640`) |
| **LLM Engine** | Ollama | **Qwen2:0.5b** (로컬 경량화 LLM) |
| **TTS Engine** | gTTS (Google TTS) | 한국어 음성 합성 API 연동 |

---

## 🛠️ 3. Skill & Data Flow

### 3.1 Project Dataflow

<p align="center">
  <img width="85%" alt="Project Dataflow" src="https://github.com/user-attachments/assets/7b29a5f4-5bbd-48a2-be6d-724128b30af8" />
</p>

---

### 3.2 Data Processing & Pipeline

<p align="center">
  <img width="85%" alt="Data Processing Flow" src="https://github.com/user-attachments/assets/7ada81c8-aaa7-4efa-a1f3-2f4ce27fd5c2" /><br>
  <b>[ Data Processing Flow ]</b>
</p>

1. **카메라 영상 입력**: 640x480 프레임 캡처
2. **다중 YOLO 추론**: TensorRT 가속 엔진 기반 실시간 객체 및 위치(좌/우/중앙, 거리) 파악
3. **위치 인지 & 데이터 파싱**: 탐지된 객체의 바운딩 박스를 분석하여 실시간 위치 정보 구조화

---

### 3.3 Speaker & Voice Output Flow

<p align="center">
  <img width="80%" alt="Speaker Flow" src="https://github.com/user-attachments/assets/236cac61-9e81-401f-8c5e-6536509ac82e" /><br>
  <b>[ Speaker 동작 흐름 ]</b>
</p>

<p align="center">
  <img width="85%" alt="Real-time Location Code" src="https://github.com/user-attachments/assets/de197f06-0af8-49d8-af35-63adf287b7f8" /><br>
  <b>[ 실시간 위치 인지 및 매핑 로직 ]</b>
</p>

---

### 3.4 Ollama (로컬 LLM) 활용

* **사용 목적**: 다중 객체 탐지 후, 단순 객체명 나열이 아닌 보행자의 상황에 맞는 자연스러운 안내 문장 생성

<p align="center">
  <img width="80%" alt="Ollama Pipeline" src="https://github.com/user-attachments/assets/80f7d7d9-58a3-4758-9b91-9e314f928844" />
</p>

- **객체 탐지 (YOLO)**: 사물의 종류 및 바운딩 박스 위치 파악
- **자연어 처리 (Ollama)**: 탐지 데이터를 기반으로 "전방 1m에 맨홀이 있으니 주의하세요"와 같은 보행자 맞춤 문장 생성
- **음성 합성 (TTS)**: 생성된 안내 텍스트를 음성 파일로 변환하여 오디오 출력

---

## 📈 4. Result

### 4.1 Data 학습 및 검증 결과

<p align="center">
  <img width="48%" alt="mAP50 Bounding Box" src="https://github.com/user-attachments/assets/6626e4e2-9720-4de2-ad52-a37ae2047f21" />
  <img width="48%" alt="Training Validation Loss" src="https://github.com/user-attachments/assets/2649516e-f2bb-4720-ba3e-6d6d24806762" />
</p>

* Bounding Box가 정확하게 형성되었으며, mAP50 성능 수치 확보
* Training 및 Validation Loss 그래프가 안정적으로 하강하며 오버피팅 없이 학습 완료

---

### 4.2 Real-Detection Examples

| Station Shelter & Manhole 탐지 | 비 오는 밤, 빨간 신호등 탐지 | 저녁 도심, 다중 위치 객체 탐지 |
| :---: | :---: | :---: |
| <img src="https://github.com/user-attachments/assets/eb6140d1-7ca1-4a62-a355-9be0620ff928" width="100%"/> | <img src="https://github.com/user-attachments/assets/0387bdd9-c4cc-44d0-8145-c3951c4dc1f8" width="100%"/> | <img src="https://github.com/user-attachments/assets/50c55d92-f977-4f09-99d3-50d9efb1ef9a" width="100%"/> |
| **버스 정류장 및 맨홀 실시간 감지** | **야간/우천 시 신호등 상태 감지** | **왼쪽/정면/오른쪽 객체들 탐지** |

#### 4.2-1 Real Detection 필요 설정 사항

| Ollama On (서버 구동) | USB Speaker Connect | Bluetooth Speaker Connect |
| :---: | :---: | :---: |
| <img src="https://github.com/user-attachments/assets/9680c06e-c288-4fd9-b5bd-20792b11d8b9" width="100%"/> | <img src="https://github.com/user-attachments/assets/e2170063-ee47-4cc5-bd7f-70dafb55ecd2" width="100%"/> | <img src="https://github.com/user-attachments/assets/b52b64c7-8203-45f7-8348-a8e52349f2b2" width="100%"/> |
| **Ollama 서비스 백그라운드 구동** | **USB 오디오 포트 연결 확인** | **블루투스 오디오 디바이스 페어링** |

---

### 4.3 한계점 및 아쉬운 점

| 표지판 오인식 사례 | 오르막길/내리막길 경사 판단 한계 |
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/7810704d-c829-4b50-bfd6-a419fc7b1987" width="100%"/> | <img src="https://github.com/user-attachments/assets/7cf21205-7d61-45cf-a49b-4a8fa01bf434" width="100%"/> |
| **일반 표지판을 정지(STOP) 표지판으로 오인식** | **2D 카메라 프레임 특성상 지형 경사도 미세 구분 아쉬움** |

1. **사물 오인식 문제**: 유사 형태의 객체(예: 일반 표지판을 정지 표지판으로 잘못 인식)에 대한 오인식 사례 존재
2. **지형 변화 인식 한계**: 오르막길, 내리막길 등 3차원 경사도 변화에 대한 미세한 판단 정확도 보완 필요

---

## 🚨 5. TroubleShooting

### 🚨 Ollama 추론 지연 시간(Latency) 문제
* **문제점**: 초기 모델인 `Gemma3:4b` 구동 시, 텍스트 생성까지 **약 5초 이상의 지연 시간**이 발생하여 실시간 보행 안내 시스템에 적용하기 어려움
* **해결 방법**: 로컬 LLM 모델을 초경량화된 **`Qwen2:0.5b`**로 변경하여 추론속도 극대화

| 코드 출력 비교 | 생성 속도 및 지연 시간 비교 |
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/7e9fceac-e3b2-437d-a233-e53a30608f5b" width="100%"/> | <img src="https://github.com/user-attachments/assets/c89b4df9-caaa-4c73-9e35-d881d5a1b0af" width="100%"/> |
| **LLM 모델 변경 후 출력 텍스트 비교** | **모델별 Response Time (Latency) 성능 수치 비교** |

* **결과**: 문장 생성 지연 시간을 수초 대에서 **실시간 대응이 가능한 수준으로 대폭 단축**하여 시각장애인 보행 환경에 적합한 빠른 피드백 체계 구축 완료
