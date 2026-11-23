---
# multilingual page pair id, this must pair with translations of this page. (This name must be unique)
lng_pair: id_license_plate_recognition
title: "불법 주정차 번호판 인식 시스템 - YOLOv5와 EasyOCR로 번호판 읽기"

# post specific
# multiple category is not supported
category: 프로젝트
# multiple tag entries are possible
tags: [Python, YOLOv5, EasyOCR, PyQt5, Oracle, 팀 프로젝트]
# thumbnail image for post
img: "/assets/img/projects/license-plate.jpg"

# publish date
date: 2024-12-08 18:00:00 +0900
---

<!-- outline-start -->

디지털영상처리 수업에서 4인 팀으로 **불법 주정차 번호판 인식 시스템**을 만들었습니다. 영상에서 차량 번호판을 찾아 잘라내고, EasyOCR로 번호를 읽어 Oracle DB에 저장한 뒤, 날짜별로 기록을 조회할 수 있는 Python 프로그램입니다. 저는 데이터셋 수집, 번호판 문자 인식 개선, 조회 GUI 구현을 맡았습니다.

<!-- outline-end -->

![번호판 탐지 결과](:lp-detect.jpg){:data-align="center"}

### 프로젝트 개요

| 항목 | 내용 |
| --- | --- |
| 기간 | 2024.09.23 ~ 2024.12.08 |
| 인원 | 4인 (디지털영상처리 팀 프로젝트) |
| 역할 | 데이터셋 수집, OCR 인식 개선, 조회 GUI |
| 기술 스택 | Python, YOLOv5, EasyOCR, OpenCV, PyQt5, Oracle |
| 개발 환경 | PyCharm, Colab |

### 동작 흐름

1. **번호판 탐지**: 직접 학습시킨 YOLOv5 모델이 영상을 10프레임마다 확인하며 번호판을 찾습니다. 신뢰도 0.6 이상인 결과만 사용합니다.
2. **이미지 추출**: 탐지된 번호판 영역을 잘라 이미지로 저장합니다.
3. **문자 인식**: EasyOCR로 잘라낸 이미지에서 번호를 읽습니다.
4. **기록 저장**: 인식한 번호와 시각을 Oracle DB에 저장합니다.
5. **조회**: GUI에서 날짜를 선택하면 그날 인식된 번호판 목록을 보여줍니다.

![번호판 탐지 코드](:lp-detect-code.png){:data-align="center"}

### 내가 맡은 부분

#### 1. 학습 데이터셋 수집

번호판 탐지 모델 학습에 쓸 데이터셋을 찾기 어려웠는데, Kaggle에서 찾았습니다.

#### 2. 번호판 문자 인식 개선

한국 번호판은 지역이 표시된 번호판처럼 **두 줄로 된 경우**가 있습니다. EasyOCR은 글자 덩어리를 찾은 순서대로 결과를 돌려주기 때문에, 그대로 이어 붙이면 번호 순서가 뒤섞일 수 있었습니다.

그래서 인식된 글자 영역의 위치를 기준으로 순서를 다시 정했습니다.

- 가장 위에 있는 영역(Y 좌표가 가장 작은 영역)을 첫 번째로 둡니다.
- 나머지 영역은 왼쪽부터(X 좌표 순서로) 정렬합니다.
- 이렇게 정렬한 글자를 이어 붙이고 공백을 제거해 최종 번호로 만듭니다.

![EasyOCR 문자 인식 코드](:lp-ocr-code.png){:data-align="center"}

#### 3. 날짜별 조회 GUI

PyQt5로 달력에서 날짜를 고르면 해당 날짜의 번호판 기록을 보여주는 GUI를 만들었습니다. 날짜를 선택하고 확인을 누르면 Oracle DB에서 기록을 불러와, 그날 인식된 번호와 인식 시각을 표로 보여줍니다.

![날짜 선택 화면](:lp-calendar.png){:data-align="center"}

![조회 결과 화면](:lp-result.png){:data-align="center"}

### 시연

최종 발표에서 이미지와 영상으로 번호판을 인식하는 결과를 시연했습니다.

### 활용 가능성

번호판 인식은 불법 주정차 단속 외에도 실시간 교통 흐름 분석, 긴급 차량 우선 통행 지원, 범죄 차량 추적처럼 교통 관리와 공공 안전 분야에 폭넓게 쓰일 수 있다고 생각합니다.
