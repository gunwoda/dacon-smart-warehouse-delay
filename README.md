# 스마트 창고 출고 지연 예측 AI

> 월간 데이콘 스마트 창고 출고 지연 예측 AI 경진대회

## 대회 개요

AMR(자율이동로봇) 기반 스마트 물류창고 운영 스냅샷 데이터를 활용하여, **향후 30분간의 평균 출고 지연 시간(분)**을 예측하는 회귀 모델 개발

- **평가 지표**: MAE (Mean Absolute Error)
- **데이터**: 12,000개 시나리오 × ~25 타임슬롯 (15분 간격), 90개 피처

## 데이터

| 파일 | 행 수 | 설명 |
|------|-------|------|
| `train.csv` | 250,000 | 학습 데이터 (90개 피처 + 타겟) |
| `test.csv` | 50,000 | 테스트 데이터 |
| `layout_info.csv` | 300 | 창고 레이아웃 보조 테이블 |
| `sample_submission.csv` | 50,000 | 제출 양식 |

## 프로젝트 구조

```
├── open/              # 원본 데이터 (git 미추적)
├── notebooks/         # EDA 및 실험 노트북
│   └── 01_EDA.ipynb
├── submissions/       # 제출 파일 (git 미추적)
├── CLAUDE.md          # AI 어시스턴트 컨텍스트
└── README.md
```

## 실행 환경

- Python 3.10+
- pandas, numpy, matplotlib, seaborn
- scikit-learn, lightgbm, xgboost, catboost
