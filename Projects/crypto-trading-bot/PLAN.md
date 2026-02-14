# 암호화폐 알고리즘 트레이딩 봇 프로젝트 계획

## Context

사용자가 알고리즘 트레이딩을 통해 암호화폐 자동매매 시스템을 구축하고자 합니다. 이는 다음을 목표로 합니다:

- **학습**: 알고리즘 트레이딩의 기본 개념과 실전 경험 습득
- **자동화**: 감정을 배제한 규칙 기반 트레이딩 시스템 구축
- **단계적 접근**: 백테스팅 → 페이퍼 트레이딩 → 소액 실전으로 안전하게 진행
- **확장성**: 향후 주식 시장(한국투자증권)으로 확장 가능한 구조

사용자는 Python 중급 수준(간단한 스크립트 작성 가능)이며, **Upbit(업비트)**을 주 거래소로 사용합니다. 초기 전략으로는 **이동평균 전략**과 **RSI 전략**을 모두 구현하여 백테스팅으로 비교 분석할 예정입니다.

## 프로젝트 구조

```
myplayground/Projects/crypto-trading-bot/
├── README.md                    # 프로젝트 개요 및 사용법
├── requirements.txt             # Python 의존성
├── .env.example                # 환경변수 템플릿 (API 키)
├── .gitignore                  # Git 제외 파일 (API 키 보호)
│
├── phase1_data_collection/     # Phase 1: 데이터 수집 및 분석
│   ├── 01_binance_api_test.py      # Binance API 연결 테스트
│   ├── 02_upbit_api_test.py        # Upbit API 연결 테스트
│   ├── 03_price_data_fetch.py      # 실시간 가격 데이터 수집
│   └── 04_technical_indicators.py  # 기술적 지표 계산 (이동평균, RSI 등)
│
├── phase2_strategy/            # Phase 2: 전략 개발 및 백테스팅
│   ├── strategies/
│   │   ├── __init__.py
│   │   ├── moving_average.py      # 이동평균 교차 전략
│   │   └── rsi_strategy.py        # RSI 기반 전략
│   ├── backtest_engine.py         # 백테스팅 엔진
│   └── analyze_results.py         # 성과 분석 및 시각화
│
├── phase3_paper_trading/       # Phase 3: 가상 거래 (모의투자)
│   ├── paper_trader.py            # 페이퍼 트레이딩 봇
│   ├── portfolio_manager.py       # 포트폴리오 관리
│   └── risk_manager.py            # 리스크 관리 (손절/익절)
│
├── phase4_live/                # Phase 4: 실전 트레이딩
│   ├── live_trader.py             # 실전 트레이딩 봇
│   ├── monitoring.py              # 모니터링 및 알림
│   └── logs/                      # 거래 로그
│
├── common/                     # 공통 유틸리티
│   ├── __init__.py
│   ├── exchange_api.py            # 거래소 API 래퍼
│   ├── indicators.py              # 기술적 지표 라이브러리
│   └── utils.py                   # 헬퍼 함수들
│
└── data/                       # 데이터 저장소
    ├── historical/                # 과거 가격 데이터
    └── trades/                    # 거래 기록
```

## 구현 계획

### Phase 1: 데이터 수집 및 API 연동 (1-2주)

**목표**: 거래소 API와 연결하고 실시간 데이터를 수집할 수 있는 기반 구축

**파일 생성**:
1. `Projects/crypto-trading-bot/README.md`
   - 프로젝트 소개, 설치 방법, 각 Phase 설명
   - Upbit API 키 발급 방법 안내
   - 안전 수칙 및 주의사항

2. `requirements.txt`
   ```
   pyupbit==0.2.31          # Upbit API (주 거래소)
   pandas==2.1.4            # 데이터 분석
   numpy==1.26.2            # 수치 계산
   matplotlib==3.8.2        # 차트 시각화
   python-dotenv==1.0.0     # 환경변수 관리
   ta==0.11.0               # 기술적 지표 라이브러리
   ```

3. `.env.example`
   ```
   # Upbit API (주 거래소)
   UPBIT_ACCESS_KEY=your_access_key_here
   UPBIT_SECRET_KEY=your_secret_key_here

   # Trading Settings
   TRADING_COIN=KRW-BTC     # 거래할 코인 (비트코인/원화)
   INITIAL_BALANCE=1000000  # 초기 가상 자금 (백테스팅용)
   ```

4. `.gitignore`
   ```
   .env
   __pycache__/
   *.pyc
   data/
   logs/
   .DS_Store
   ```

5. `phase1_data_collection/01_upbit_api_test.py`
   - Upbit API 연결 테스트
   - 원화 마켓 리스트 조회 (KRW-BTC, KRW-ETH 등)
   - 현재 BTC/KRW 가격 조회
   - 계정 잔고 조회 (API 키 필요)

6. `phase1_data_collection/02_price_data_fetch.py`
   - 과거 가격 데이터 수집 (일봉, 분봉 캔들 데이터)
   - CSV 파일로 저장 (data/historical/에 저장)
   - 데이터 확인 및 시각화

7. `phase1_data_collection/03_realtime_price.py`
   - 실시간 가격 모니터링
   - WebSocket을 이용한 실시간 틱 데이터 수집 (선택사항)

8. `phase1_data_collection/04_technical_indicators.py`
   - 이동평균(MA) 계산
   - RSI (Relative Strength Index) 계산
   - MACD 계산
   - 볼린저 밴드 계산

### Phase 2: 전략 개발 및 백테스팅 (2-3주)

**목표**: 이동평균과 RSI 두 가지 전략을 개발하고 과거 데이터로 성과 비교

**파일 생성**:
1. `phase2_strategy/strategies/moving_average.py`
   - **골든크로스/데드크로스 전략**
   - 단기MA(5일) > 장기MA(20일): 매수 신호
   - 단기MA < 장기MA: 매도 신호
   - 파라미터 최적화 가능 (MA 기간 조정)

2. `phase2_strategy/strategies/rsi_strategy.py`
   - **RSI 기반 역추세 전략**
   - RSI < 30: 과매도 구간 → 매수 신호
   - RSI > 70: 과매수 구간 → 매도 신호
   - RSI 기간: 14일 (표준)

3. `phase2_strategy/backtest_engine.py`
   - 과거 데이터로 두 전략 각각 시뮬레이션
   - 수익률, 승률, MDD(최대 낙폭), 샤프 비율 계산
   - Upbit 수수료(0.05%) 반영
   - 거래 로그 상세 기록

4. `phase2_strategy/compare_strategies.py`
   - **두 전략의 성과 비교 분석** (핵심!)
   - 수익률 비교 차트
   - 승률, MDD, 샤프 비율 비교 테이블
   - 어떤 시장 상황에서 어떤 전략이 유리한지 분석
   - 결과를 표와 그래프로 시각화

### Phase 3: 페이퍼 트레이딩 (1-2주)

**목표**: 실제 돈 없이 가상으로 전략 실행하며 검증

**파일 생성**:
1. `phase3_paper_trading/paper_trader.py`
   - 실시간 데이터로 전략 실행 (실제 주문은 하지 않음)
   - 가상 잔고 관리
   - 거래 로그 기록

2. `phase3_paper_trading/portfolio_manager.py`
   - 포트폴리오 관리 (여러 코인 동시 거래)
   - 포지션 사이징 (전체 자산의 몇 %를 거래할지)

3. `phase3_paper_trading/risk_manager.py`
   - 손절매(Stop Loss) 로직
   - 익절매(Take Profit) 로직
   - 최대 손실 한도 설정

### Phase 4: 실전 트레이딩 (소액으로 조심스럽게)

**목표**: 소액으로 실전 자동매매 시작

**파일 생성**:
1. `phase4_live/live_trader.py`
   - 실제 거래소 API로 주문 실행
   - 자동 재시작 및 에러 핸들링
   - 거래 전 안전 체크

2. `phase4_live/monitoring.py`
   - 실시간 성과 모니터링
   - 텔레그램/이메일 알림
   - 긴급 종료 기능

### 공통 모듈

**파일 생성**:
1. `common/exchange_api.py`
   - Binance, Upbit API 통합 인터페이스
   - 공통 메서드: get_price(), place_order(), get_balance()

2. `common/indicators.py`
   - 재사용 가능한 기술적 지표 함수들
   - Phase 1에서 개발한 코드 모듈화

3. `common/utils.py`
   - 날짜/시간 처리
   - 로깅 설정
   - 설정 파일 로드

## 검증 방법

### Phase 1 검증
```bash
# 프로젝트 폴더로 이동
cd myplayground/Projects/crypto-trading-bot

# 가상환경 생성 및 활성화
python3 -m venv venv
source venv/bin/activate  # Mac/Linux

# 의존성 설치
pip install -r requirements.txt

# .env 파일 생성 (API 키 입력)
cp .env.example .env
# .env 파일을 열어서 Upbit API 키 입력 필요

# Upbit API 테스트
python phase1_data_collection/01_upbit_api_test.py

# 과거 데이터 수집 테스트
python phase1_data_collection/02_price_data_fetch.py

# 실시간 가격 모니터링 (선택)
python phase1_data_collection/03_realtime_price.py

# 기술적 지표 계산 테스트
python phase1_data_collection/04_technical_indicators.py
```

**기대 결과**:
- Upbit API 연결 성공, 현재 BTC/KRW 가격 출력
- `data/historical/` 폴더에 CSV 파일 생성 (최근 200일치 일봉 데이터)
- 이동평균(5일, 20일), RSI 등 지표 값 출력 및 차트 표시

### Phase 2 검증
```bash
# 백테스팅 실행
python phase2_strategy/backtest_engine.py

# 결과 분석
python phase2_strategy/analyze_results.py
```

**기대 결과**:
- 백테스팅 수익률 리포트 생성
- 차트에 매수/매도 시그널 표시
- 전략별 성과 비교 테이블

### Phase 3 검증
```bash
# 페이퍼 트레이딩 시작
python phase3_paper_trading/paper_trader.py
```

**기대 결과**:
- 실시간으로 전략 실행되며 로그 출력
- 가상 잔고 변화 확인
- 손절/익절 동작 확인

### Phase 4 검증 (신중하게!)
```bash
# 실전 봇 실행 (소액으로!)
python phase4_live/live_trader.py
```

**기대 결과**:
- 실제 주문 체결 확인
- 모니터링 대시보드에서 실시간 확인
- 알림 수신 확인

## 중요 참고사항

1. **API 키 보안**: `.env` 파일은 절대 Git에 커밋하지 않음 (`.gitignore`에 포함)
2. **단계별 진행**: 각 Phase를 충분히 테스트한 후 다음 단계로 진행
3. **리스크 관리**: Phase 4에서는 반드시 소액(10만원 이하)으로 시작
4. **지속적 모니터링**: 봇을 실행하는 동안 정기적으로 성과 확인
5. **백테스팅 함정**: 과거 성과가 미래를 보장하지 않음 (슬리피지, 시장 변화 고려)

## 예상 일정

- **Week 1-2**: Phase 1 완료 (API 연동, 데이터 수집)
- **Week 3-5**: Phase 2 완료 (전략 개발, 백테스팅)
- **Week 6-7**: Phase 3 완료 (페이퍼 트레이딩)
- **Week 8+**: Phase 4 (실전 트레이딩 - 소액, 신중하게)

## 다음 단계 (구현 후)

1. **성과 분석**: 실전 거래 데이터 분석 및 전략 개선
2. **다중 전략**: 여러 전략을 조합하여 포트폴리오 구성
3. **머신러닝**: 가격 예측 모델 도입 (선택사항)
4. **주식 확장**: 한국투자증권 API로 주식 자동매매 확장
