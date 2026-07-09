🚀 포트폴리오용 모델 파이프라인 요약
1. 📊 데이터 수집 (Data Collection)
데이터 소스: Yahoo Finance API (yfinance)
대상 자산: 비트코인 (BTC/USD)
타임프레임 (해상도): 15분봉 (15-minute intervals)
수집 기간: 최근 60일 (충분한 학습 데이터 확보 및 단기 트렌드 반영)
기본 특성: Open, High, Low, Close, Volume (OHLCV)
2. 🛠️ 피처 엔지니어링 (Feature Engineering)
단순한 단기 지표뿐만 아니라 다중 타임프레임(MTF)을 결합하여 시장의 노이즈를 필터링하고 거시적인 추세를 모델에 학습시켰습니다.

기본 기술적 지표:
Returns: 직전 캔들 대비 수익률
SMA_7: 7기간 단순 이동평균선
RSI_14: 상대강도지수 (과매수/과매도 파악)
BB_Width: 볼린저 밴드 너비 (변동성 수축/확산 파악)
MTF (Multi-Timeframe) 추세 및 변동성 지표:
SMA_1H (1시간 추세): 4기간(15분×4) 이동평균
SMA_4H (4시간 추세): 16기간(15분×16) 이동평균
SMA_24H (일일 추세): 96기간(15분×96) 이동평균
Vol_4H (4시간 변동성): 16기간 수익률의 표준편차
3. 🎯 목표 변수 설정 (Target Definition - 3 Class Classification)
비트코인의 잦은 휩소(가짜 움직임)에 당하지 않도록, 유의미한 변동성이 있을 때만 타점을 잡도록 임계값(Threshold)을 설정했습니다.

예측 목표: 다음 15분 캔들의 종가 변화율
임계값 (Threshold): ±0.1% (0.001)
3-Class Labeling:
Class 2 (Long / 상승): 다음 캔들에서 +0.1% 초과 상승
Class 0 (Short / 하락): 다음 캔들에서 -0.1% 미만 하락
Class 1 (Neutral / 횡보): 변동폭이 ±0.1% 이내일 경우 (관망)
4. 🧠 모델 학습 및 평가 (Model Training & Evaluation)
알고리즘: XGBoost Classifier (트리 기반 앙상블 모델)
비선형적인 시장 데이터 패턴을 포착하고 결측치/이상치에 강건한 모델 채택.
학습 데이터 분할: 시계열 데이터의 특성을 고려하여 과거 데이터 90%를 Train(학습) 데이터로, 최근 10%를 Test(검증) 데이터로 분할 (Time-Series Split).
주요 하이퍼파라미터:
n_estimators=200, learning_rate=0.05, max_depth=5 (과적합 방지)
subsample=0.8, colsample_bytree=0.8 (일반화 성능 향상)
objective='multi:softprob' (단순한 방향 예측을 넘어 각 클래스별 확률을 출력하도록 설정)
의의 및 차별점:
딥러닝(CNN-LSTM) 모델에서 발생하던 과적합 문제를 해결하기 위해 머신러닝(XGBoost)으로 경량화 및 최적화 진행.
단순히 오르고 내리는 것을 맞추는 2진 분류가 아니라, '횡보장(Neutral)'을 걸러내는 3진 분류를 채택하여 실제 매매 시 승률과 손익비를 높이는 실전형 아키텍처 구현.
