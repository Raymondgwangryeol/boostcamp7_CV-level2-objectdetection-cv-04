# 🗑️ Waste Image Object Detection

> **Boostcamp AI Tech 7기 | Computer Vision 프로젝트**
>
> 분리수거 쓰레기를 이미지 기반으로 자동 분류/탐지하는 Object Detection 프로젝트입니다. 

## 프로젝트 소개

오늘날 우리는 대량 생산과 소비가 일상화된 사회에 살고 있으며, 이로 인해 폐기물 증가와 자원 고갈, 매립지 부족 등 다양한 환경 문제가 심화되고 있습니다.

분리수거는 이러한 문제를 완화할 수 있는 효과적인 방법 중 하나지만, 잘못된 분리배출은 재활용률을 저하시키고 오히려 환경 부담을 가중시킬 수 있습니다.

본 프로젝트는 이미지 기반 객체 탐지 기술을 활용하여, 쓰레기 종류를 자동으로 인식하고 분류할 수 있는 AI 모델을 개발하는 것을 목표로 했습니다.
모델 학습에는 일반 쓰레기, 플라스틱, 종이, 유리 등 총 10종의 쓰레기 객체가 포함된 이미지 데이터셋을 활용하였습니다.

## 사용 기술

- Object Detection: `Detectron2`, `MMDetection2.X`
- 학습 프레임워크: `PyTorch`
- 실험 관리: `Branch 기반 실험 관리`, `Google Sheet + Notion 기록`
- 데이터 포맷: `bbox 중심 labeling`
- 기타: `Albumentations`, `timm`

## 기여한 부분

EfficientDet 도입 및 실험 주도
- EfficientNet 기반의 경량성과 BiFPN 구조를 활용한 EfficientDet 모델 선정
- 다양한 크기의 쓰레기 객체 탐지에 적합하다고 판단하여 실험 설계 및 주도적 실행

성능 개선 시도
- 초기 실험에서 낮은 mAP50 (~0.178) 성능을 기록
- Config 설정 및 anchor box 크기 변경 등 다양한 하이퍼파라미터 조정 시도
- 반복 학습 및 실험 로그 분석을 통해 성능 개선 방안 모색
- 리소스 및 시간 제약 속에서도 최선의 성능을 도출하기 위해 지속적으로 실험 진행

데이터 증강 전략 실험
- Mosaic + CenterCrop: 객체가 중앙에 몰려있는 데이터셋 특성 활용
- Mosaic + CutMix: metal, plastic 등 유사 클래스 간 구분력 향상을 위해 설계
- augmentation 조합별 성능 변화 분석 및 실험 결과 정리

실험 실패를 통한 인사이트 도출
- 실험 결과가 기대에 미치지 못했음에도 불구하고, 모델 구조 및 증강 전략에 대한 깊은 이해 확보
- 제한된 자원 속에서 실험 계획 수립 및 효율적인 리소스 배분 경험 축적

## Project Structure
```
project_root/
│
├── dataset/
│   ├── test/
│   ├── train/
│   ├── test.json
│   └── train.json
│
├── detectron2/
│   ├── detectron2 folders
│   ├── train.py
│   └── inference.py
│
├── pytorch_detection/
│   ├── train.py
│   ├── inference.py
│   └── src/
│      ├── config.py
│      ├── inference.py
│      ├── model.py
│      ├── trainer.py
│      └── utils.py
│
├── mmdetection/
│   ├── mmdetection folders
│   ├── train.py
│   └── inference.py
│
├── requirements.txt
└── README.md
```

## Model Architecture

본 프로젝트는 **MMDetection v3**를 기반으로 진행되었습니다.

### 모델 구성

#### 1. EfficientNet-d3 + RetinaNet
- **Backbone**: EfficientNet-d3
- **Head**: RetinaNet (`RetinaSepBNHead`)
- **Input Image Size**: 896 × 896
- **Augmentation (Train)**:
  - RandomResize
  - RandomCrop
  - RandomFlip

#### 2. YOLOv10_n
- **Backbone**: 경량화 버전 (deepen_factor=0.33, widen_factor=0.25, use_depthwise=True)
- **Neck**:
  - in_channels: [64, 128, 256]
  - out_channels: 64
  - num_csp_blocks: 1
  - use_depthwise: True
- **Head (bbox_head)**:
  - in_channels: 64
  - feat_channels: 64
  - use_depthwise: True

## Training Script
본 프로젝트에서는 mmdetection3의 기본 train.py를 기반으로, 실험에 맞게 수정한 커스텀 스크립트를 사용했습니다.

### 주요 변경사항
- Config 파일 수정: 모델 구조, 데이터 경로, 클래스 수, optimizer 설정 등을 실험에 맞게 동적으로 수정할 수 있도록 했습니다.
- Argument 추가: --epochs, --batch-size, --lr, --num-classes, --data-root, --work-dir 등 주요 학습 설정을 인자로 받아 설정할 수 있도록 변경했습니다.
- Flexible한 실험: 다양한 모델 및 데이터셋 환경에서 손쉽게 실험을 반복할 수 있도록 하였습니다.
- Runner 사용: 수정된 config를 기반으로 Runner를 생성하고 학습을 진행합니다.

### 스크립트 특징
- EfficientNet 기반 모델과 YOLOv10 모델 실험 모두 이 스크립트를 통해 학습을 수행했습니다.
- 데이터셋 경로와 클래스 수를 쉽게 변경할 수 있어 다양한 환경에서 재사용이 가능합니다.


   
