# CLAUDE.md

## 대회 개요

**월간 데이콘 스마트 창고 출고 지연 예측 AI 경진대회**

AMR(자율이동로봇) 기반 스마트 물류창고 운영 스냅샷 데이터를 활용하여, 현재 시점으로부터 **향후 30분간의 평균 출고 지연 시간(분)**을 예측하는 회귀 모델 개발.

- 12,000개 독립 시나리오, 각 ~25개 타임슬롯(15분 간격, 약 6시간)
- 비선형적 지연 패턴 (주문 유입, 로봇 운영, 배터리/충전, 혼잡도, 패킹 병목 등)

## 데이터

| 파일 | 경로 | 행 수 | 설명 |
|------|------|-------|------|
| train.csv | `open/train.csv` | 250,000 | 학습 데이터 (90개 피처 + 타겟) |
| test.csv | `open/test.csv` | 50,000 | 테스트 데이터 (타겟 없음) |
| layout_info.csv | `open/layout_info.csv` | 300 | 창고 레이아웃 보조 테이블 (layout_id 기준 조인) |
| sample_submission.csv | `open/sample_submission.csv` | 50,000 | 제출 양식 (ID, avg_delay_minutes_next_30m) |

### 타겟 변수
- `avg_delay_minutes_next_30m` : 향후 30분간 출고 주문의 평균 지연 시간(분)

### 키 컬럼
- `ID` : 고유 식별자 (TRAIN_XXXXXX / TEST_XXXXXX)
- `layout_id` : 창고 레이아웃 ID → layout_info.csv 조인 키
- `scenario_id` : 시나리오 ID (같은 시나리오 = 같은 창고의 시계열)

### 주요 피처 그룹 (90개)
- **주문**: order_inflow_15m, unique_sku_15m, avg_items_per_order, urgent_order_ratio, heavy_item_ratio, cold_chain_ratio, sku_concentration
- **로봇 운영**: robot_active, robot_idle, robot_charging, robot_utilization, avg_trip_distance, task_reassign_15m
- **배터리/충전**: battery_mean, battery_std, low_battery_ratio, charge_queue_length, avg_charge_wait
- **혼잡도**: congestion_score, max_zone_density, blocked_path_15m, near_collision_15m
- **장애**: fault_count_15m, avg_recovery_time
- **패킹/출고**: replenishment_overlap, pack_utilization, manual_override_ratio
- **환경**: warehouse_temp_avg, humidity_pct, external_temp_c, wind_speed_kmh 등
- **IT 인프라**: wms_response_time_ms, scanner_error_rate, wifi_signal_db, network_latency_ms
- **KPI/운영**: kpi_otd_pct, backorder_ratio, sort_accuracy_pct 등

### layout_info.csv 피처
- layout_type (narrow/grid/hybrid), aisle_width_avg, intersection_count, one_way_ratio
- pack_station_count, charger_count, layout_compactness, zone_dispersion
- robot_total, building_age_years, floor_area_sqm, ceiling_height_m 등

### 데이터 특성
- **결측치 다수 존재** (빈 값 = NaN)
- 시나리오 내 시계열 순서 존재 (scenario_id 기준 그룹)

## 평가 지표

- **MAE (Mean Absolute Error)** — 낮을수록 좋음

## 프로젝트 구조

```
dacon/jyb/
├── CLAUDE.md          # 이 파일
├── open/              # 원본 데이터
│   ├── train.csv
│   ├── test.csv
│   ├── layout_info.csv
│   └── sample_submission.csv
├── notebooks/         # EDA 및 실험 노트북 (필요시 생성)
└── submissions/       # 제출 파일 (필요시 생성)
```

## 개발 규칙

- Python 사용, 주요 라이브러리: pandas, numpy, scikit-learn, lightgbm, xgboost, catboost
- 제출 파일은 `sample_submission.csv`와 동일한 형식 (ID, avg_delay_minutes_next_30m)
- 코드 작성 시 재현성 확보: random seed 고정 (seed=42)
- layout_info.csv는 layout_id 기준으로 train/test에 merge하여 사용
- 시나리오 내 시계열 특성 활용 고려 (lag feature, rolling feature 등)
