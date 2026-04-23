# graduation_project
# 자율지게차 자료 모음

 자율 지게차 프로젝트 — 파이프라인별 논문 정리

> **프로젝트**: TurtleBot3 기반 자율 지게차 모형 (2D LiDAR + CS20 ToF + RGB Camera) **아키텍처**: Coarse-to-Fine 3단계 팔레트 인식 + 자율주행 + 포크 삽입 **최종 수정**: 2026-04-13
> 

---

## 전체 시스템 아키텍처

[Stage 0] 데이터셋 준비
├── ToF(CS20) 360° 스캔 → 레퍼런스 3D 모델
└── RGB 다거리/다각도 촬영 → YOLOv8-pose 학습
↓
[Stage 1] 자율주행 (2D LiDAR SLAM + Nav2)
├── SLAM 지도 생성 (GMapping / Cartographer)
├── 전방 장애물 인식 및 회피 (DWA / TEB)
├── 목표 팔레트 선택
└── 목표 영역까지 주행
↓
[Stage 2] Coarse Perception — RGB 기반 (원거리 ~3m)
├── RGB 카메라 입력
├── YOLOv8 기반 팔레트 / 포크홀 검출
├── 코너 / 특징점 추출
├── PnP 기반 Rough 6DoF Pose 추정
└── 팔레트 전면 근처까지 접근
↓
[Stage 3] Fine Perception — ToF Point Cloud 기반 (근거리 ~0.5m)
├── CS20 ToF → Point Cloud 획득
├── FPFH 특징 계산
├── SAC-IA → Point Cloud Coarse Registration
└── 정밀 Pose 추정
↓
[Stage 4] Ultra-Fine Alignment + 삽입 — ICP 기반
├── ICP 기반 Final Fine Alignment
├── 포크 정렬 보정
└── 포크홀 삽입

---

## Stage 0 — 데이터셋 준비

| # | 논문 | 저자 / 연도 | 핵심 내용 |
| --- | --- | --- | --- |
| 0-1 | Pallet Detection and Localisation from Synthetic Data | Mueller et al., 2025 | YOLOv8-pose로 합성 데이터에서 팔레트 코너 검출 + PnP pose 추정. Unity 합성 데이터 7,140장 + Isaac Sim Domain Randomization 50,000장 |
| 0-2 | Improving Pallet Detection Using Synthetic Data | Mueller et al., 2024 | Unity + NVIDIA Isaac Sim으로 합성 데이터 생성, YOLOv8 팔레트 검출 훈련 비교 |
| 0-3 | 3D Shape Scanning with a Time-of-Flight Camera | Schuon et al., 2010 | ToF 카메라 다방향 스캔 → superresolution + 확률적 정합으로 3D 모델 구축 |
| 0-4 | A Comprehensive Review of Vision-Based 3D Reconstruction Methods | Zhou et al., 2024 | ToF 기반 3D 복원 방법 전반 서베이 (Sensors) |
| 0-5 | Synthetica: Large Scale Synthetic Data for Robot Perception | NVIDIA, 2024 | Isaac Sim + Omniverse Replicator로 대규모 합성 데이터 생성 파이프라인 |

---

## Stage 1 — 자율주행 (2D LiDAR SLAM + 장애물 회피)

| # | 논문 | 저자 / 연도 | 핵심 내용 |
| --- | --- | --- | --- |
| 1-1 | Autonomous navigation of ROS2 based Turtlebot3 in static and dynamic environments | Gyanani et al., 2025 | TurtleBot3에서 TD3/DDPG/DQN 강화학습 + LiDAR SLAM + Nav2 자율주행 |
| 1-2 | Autonomous Mobile Vehicle Using ROS2 and 2D-Lidar and SLAM Navigation | Gyanani et al., 2024 | ROS2 + 2D LiDAR SLAM + Nav2 자율주행 구현 |
| 1-3 | ROS-Based Navigation and Obstacle Avoidance: A Study of Architectures, Methods, and Trends | 2025 서베이 | DWA, TEB 등 ROS 기반 장애물 회피 비교 분석. TurtleBot3/Jetson Nano 경량화 이슈 포함 |
| 1-4 | SimNav-XR: Extended reality platform for mobile robot simulation using ROS2 and Unity3D | 2026 | TurtleBot3 + ROS2 Humble + Cartographer SLAM + Nav2 시뮬레이션 |

---

## Stage 2 — Coarse Perception (RGB 기반 Rough 6DoF)

| # | 논문 | 저자 / 연도 | 핵심 내용 |
| --- | --- | --- | --- |
| 2-1 | **Pallet Pose Estimation Based on Front Face Shot (FFS)** | Kai et al., 2025 (IEEE Access) | YOLO → CNN(WithBNet) 전면코너 검출 → KRR 후면코너 추정 → PnP 6DoF. 적재물 외관 변화에 강건. **총 추론 49.2ms** |
| 2-2 | Visual and 3D Point Cloud Fusion for Pallet Detection | IEEE ICMTIM, 2024 | 개선 YOLOv8 검출 → RGB+Depth 융합 → 정밀 3D 위치 추정 |
| 2-3 | **Pallet recognition and localization using an RGB-D camera** | Xiao et al., 2017 | RGB-D 깊이 데이터 평면 세그먼테이션 → 템플릿 매칭 팔레트 인식 → 위치/yaw 추정. 다종 팔레트 대응 |
| 2-4 | Pallet Detection and Distance Estimation with YOLO and Fiducial Marker | Kesuma et al., 2023 | YOLOv5 포크홀 검출 + ArUco 마커 pose/거리 추정. 평균 오차 2.28cm |
| 2-5 | Hypergraph Computing Framework for Forklift Pallet Pose Estimation | Ye et al., 2026 | 하이퍼그래프로 팔레트 keypoint 고차 공간관계 모델링, 차폐 환경 강건 |
| 2-6 | 6-DoF Object Pose from Semantic Keypoints | Pavlakos et al., 2017 (ICRA) | CNN semantic keypoint + deformable shape model + PnP 6DoF 추정 (원론) |
| 2-7 | PVNet: Pixel-wise Voting Network for 6DoF Pose Estimation | Peng et al., 2019 (CVPR) | 픽셀 단위 투표 → 2D keypoint 검출 → uncertainty-driven PnP. 차폐에 강건 |

---

## Stage 3 — Fine Perception (ToF Point Cloud 기반 정밀 Pose)

| # | 논문 | 저자 / 연도 | 핵심 내용 |
| --- | --- | --- | --- |
| 3-1 | **Fast Point Feature Histograms (FPFH) for 3D Registration** | Rusu et al., 2009 (ICRA) | FPFH 원본 논문. PFH → O(n·k) 간소화, SAC-IA 초기 정합 알고리즘 제안 |
| 3-2 | **A Point Cloud Data-Driven Pallet Pose Estimation (AGWF)** | Shao et al., 2023 (Sensors) | FPFH 개선 AGWF 제안 → SAC-IA coarse → ICP fine → 팔레트 6DoF. 정확도 35%↑, 시간 30%↓ |
| 3-3 | **Point cloud registration based on multiple-local-feature matching** | Guo et al., 2023 (Optik) | FPFH 매칭 후 다중 이웃 공분산+곡률로 오매칭 필터링 → RANSAC coarse → ICP fine |
| 3-4 | A Novel Pallet Detection Method for AGV (ACFPFH) | Shao et al., 2022 (Sensors) | HSV 색상 + FPFH 기하학 결합한 ACFPFH → RANSAC coarse → ICP fine |

---

## tage 4 — Ultra-Fine Alignment + 포크 삽입 (ICP 기반)

| # | 논문 | 저자 / 연도 | 핵심 내용 |
| --- | --- | --- | --- |
| 4-1 | **A Method for Registration of 3-D Shapes (ICP)** | Besl & McKay, 1992 (IEEE TPAMI) | ICP 원본 논문. 최근접점 반복 → 평균제곱오차 단조 수렴 증명 |
| 4-2 | ICP-Based Pallet Tracking for Unloading on Inclined Surfaces | 2024/2026 | ICP로 팔레트 상부 포인트클라우드 실시간 추적. 포크-팔레트 상대자세 수렴 0.25° 이내 |
| 4-3 | Approach and Fork Insertion to Target Pallet Based on Image Measurement | Kai et al., 2025 (Sensors) | 광각 이미지 기반 접근~포크 삽입 전과정 자동화 실내 검증 |

---

## 전체 파이프라인 참고 (End-to-End System)

| # | 논문 | 저자 / 연도 | 핵심 내용 |
| --- | --- | --- | --- |
| R-1 | Lang2Lift: Language-Guided Autonomous Forklift System | Nguyen et al., 2025/2026 | Florence-2 + SAM2 + FoundationPose → 6D pose → 자율 포크 삽입. ROS2 기반 closed-loop |
| R-2 | ADAPT: Autonomous Forklift for Construction Site | Huemer et al., 2025 | 자율 야외 포크리프트 전체 시스템 (주행+인식+조작) |
| R-3 | Autonomous Forklifts: State of the Art Survey | 2025 서베이 | 자율 포크리프트 센서/검출/시스템 종합 서베이 |

---
