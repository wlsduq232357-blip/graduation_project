# 데이터셋 구축 및 전처리 기법(RANSAC)

데이터셋 구축 비율

[]()

**단순 객체 검출이 아니라 키포인트 기반 자세 추정**

팔레트가 쌓여 있거나 가려지고, 조명이 바뀌고, 배경이 복잡해서 단순 객체 검출만으로는 정확한 위치 추정이 어려움. 특히 자동 지게차는 포크를 정확히 넣어야 하므로, 단순히 팔레트의 위치를 보는 수준이 아니라 팔레트의 자세와 위치를 정밀하게 알아야 함. 때문에 이 논문에서는 팔레트 전면 E 단면의 핵심 점들을 찾음

논문의 핵심 아이디어는 사람 자세 추정 방식의 키포인트 추출을 팔레트에 적용함. 팔레트 전면 E 구조에서 12개 키포인트를 검출하도록 설계함. 이 키포인트들 사이의 기하학적 관계를 이용해 팔레트의 기울기와 거리를 계산함.

<img width="416" height="624" alt="Image" src="https://github.com/user-attachments/assets/14533d2d-8494-4d43-8e18-09b36232d6a5" />

데이터셋 : 원본 4,125 / 데이터 증강 후 12,112 장의 팔레트 이미지를 사용했고,

70% train / 20% validation / 10% test로 분할하였다. 이미지에는 실내ㆍ실외, 높이 변화, 가림 같은 상황이 포함됨. 서로 다른 조명, 각도, 거리에서 촬영하였음.

Pallet and Pocket Detection Based on Deep Learning Techniques(RANSAC 사용 논문)

RANSAC란?

RANSAC은 노이즈와 많고 이상치가 섞여 있어도, 데이터 안에서 의미 있는 기하학적 구조를 찾기 위한 방법

핵심은 전체 데이터를 한 번에 사용하는 것이 아니라, 일부 점을 무작위로 뽑아 하나의 모델을 만든 뒤, 그 모델에 잘 맞는 점이 얼마나 많은지 반복적으로 확인하는 방식

먼저 YOLOv8으로 팔레트 영역을 찾고, 그다음 depth/point cloud를 이용해 팔레트 표면을 나눈다. 이 과정에서 논문은 RANSAC으로 여러 평면을 분리

이 논문에서 RANSAC의 역할

입력: 팔레트, 바닥, 벽, 배경이 함께 들어 있는 point cloud

처리: RANSAC으로 여러 평면을 나눔

목적: 바닥이나 벽 같은 불필요한 부분을 줄이고, 팔레트 표면을 더 잘 찾기 위함

<img width="286" height="779" alt="Image" src="https://github.com/user-attachments/assets/d1d6842d-0cdc-4723-b320-f76843e4ef75" />

- **Bounding boxes segmentation**
2D에서 찾은 박스 영역에 해당하는 점군만 잘라냄
- **Filtering based on pallet class**
팔레트 클래스에 해당하는 결과만 남김
- **Segment selection based on highest confidence score**
여러 후보 중 신뢰도가 가장 높은 것을 선택
- **Voxel grid filtering**
점 개수를 줄여서 계산을 가볍게 만듦
- **RANSAC plane segmentation**
점군 안의 평면들을 나눕니다.
여기서 팔레트 전면이나 바닥 같은 평면 구조를 구분
- **Plane selection based on area**
나뉜 평면 중에서 면적 기준으로 적절한 평면을 선택
- **Statistical outlier removal**
주변과 동떨어진 이상한 점들을 제거

RANSAC은 팔레트를 직접 인식하는 마지막 단계의 알고리즘이 아니라, 팔레트를 더 잘 찾기 위해 point cloud를 정리하는 전처리 단계에 사용
