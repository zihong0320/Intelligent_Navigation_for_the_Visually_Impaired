# Intelligent Walking Navigation for the Visually Impaired

## 0. Summary

### 🎯 Overview
- 다중 YOLO을 사용하여 장애물(ex. 사람, 개, 신호등, 교통수단, 횡단보도, 계단 등)에 대해 높은 신뢰성의 실시간 분류 및 탐지
- 분류 및 탐지된 장애물의 위치 정보에 따른 상황 맞춤별 안내 문자 생성
- 유무선(USB/Bluetooth) 스피커를 활용한 실시간 음성 출력

### 🛠 개발 환경 및 Architecture
* **Target Board**: Jetson Orin Nano
* model : YOLO
* ollama(local LLM)
* **Development Tools**: VS Code
* **Languages**: Python
* cam, speaker(bluetooth, USB)

---

### 담당역할
- 코드 총괄
- OLLAMA 적용
- 다중 객체 탐지 및 Bluetooth speaker 음성 출력
---

### 구현목표
  - YOLO 모델을 활용한 실시간 다중 객체 분류 및 탐지
  - Jetson 보드 내 Ollama(로컬 LLM) 구축 및 Ollama API와 파이썬 연동
  - 오디오 출력 지원

## 1. Introduction
### 1.1 YOLO
  - YOU ONLY LOOK ONCE
  - 이미지 전체를 보고 사물을 찾아내는 고속 실시간 객체 탐지 인공지능 모델

### 1.2 OLLAMA
  - 대규모 AI 언어 모델(LLM)을 로컬 기기에서 무료로 다운로드하고 구동할 수 있게 해주는 오픈 소스 인프라 플랫폼


## 2. Specification
### 2.1 Hardware Specification
Main board
- Jetson Orin Nano Developer Kit
- NVIDIA Ampere architecture GPU,
- 블루투스 내장
- 온디바이스(On-Device) 실시간 다중 YOLO 추론 및 로컬, LLM(Ollama) 구동 핵심 연산 장치 

Camera
- Logitech C270 HD Webcam
- HD 720p (최대 1280x720) 지원
- 프로젝트 설정: 640x480 pixels
- 실시간 보행 환경 영상 캡처 및 AI 모델 프레임 공급


Audio
- Bluetooth 스피커, USB 스피커 
- 시각장애인 대상 실시간 상황 안내 음성(gTTS) 출력



### 2.2 Software Specification
Language
- Python

AI Framework
- Ultralytics YOLO11
- TensorRT (.engine) 포맷 변환 사용

AI Models
- 다중 Object Detection
  • coco_model - (imgsz=320)
  • custom_model_1 - (imgsz=640)
  • custom_model_2 - (imgsz=640)
  • custom_model_3 - (imgsz=640)


LLM Engine
- Ollama 
- Gemma3:4b (로컬 경량화 LLM) -> Qwen2:0.5b


TTS Engine
- gTTS (Google TTS)
- 온라인 API 기반 한국어 지원

## 3. Skill

### 3.1 Project Dataflow

<img width="937" height="447" alt="image" src="https://github.com/user-attachments/assets/7b29a5f4-5bbd-48a2-be6d-724128b30af8" />



## 4. Result
### 4.1 Data 처리

<img width="609" height="284" alt="image" src="https://github.com/user-attachments/assets/6626e4e2-9720-4de2-ad52-a37ae2047f21" />
<img width="819" height="346" alt="image" src="https://github.com/user-attachments/assets/2649516e-f2bb-4720-ba3e-6d6d24806762" />

- Bounding Box 잘 잡음(mAP50)
- Training, Validation Loss 잘 줄어듦


### 4.2 Real-Detection

<img width="995" height="366" alt="image" src="https://github.com/user-attachments/assets/eb6140d1-7ca1-4a62-a355-9be0620ff928" />
<Station Shelter, Manhole 탐지>

<img width="898" height="407" alt="image" src="https://github.com/user-attachments/assets/0387bdd9-c4cc-44d0-8145-c3951c4dc1f8" />
<비오는 밤, 빨간 신호등 탐지>

<img width="969" height="402" alt="image" src="https://github.com/user-attachments/assets/50c55d92-f977-4f09-99d3-50d9efb1ef9a" />
<저녁 도심, 다중 위치의 객체 탐지>

#### 4.2-1 Real Detection 필요 설정 사항


















