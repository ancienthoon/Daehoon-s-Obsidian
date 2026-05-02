# KRX Paper Trading Bot — 전체 코드

---

## 📄 `requirements.txt`

```text
# 핵심 데이터 처리
pandas>=2.0.0
numpy>=1.24.0

# 한국 주식 데이터
pykrx>=0.1.0
FinanceDataReader>=0.9.0

# 기술적 지표
pandas-ta>=0.3.14b0

# 시각화
matplotlib>=3.7.0
plotly>=5.15.0

# 스케줄링
schedule>=1.2.0

# 설정 관리
python-dotenv>=1.0.0
PyYAML>=6.0

# 테스트
pytest>=7.4.0
pytest-mock>=3.11.0

# 유틸리티
python-dateutil>=2.8.0
```

---

## ⚙️ `config/settings.yaml`

```yaml
# 전체 설정 파일
# 모든 매직 넘버는 여기서 관리합니다.

system:
  name: "KRX Paper Trading Bot"
  version: "1.0.0"
  timezone: "Asia/Seoul"

data:
  # 데이터 수집 관련
  source: "pykrx"               # pykrx 또는 finance_datareader
  start_date: "2023-01-01"
  end_date: null                # null이면 오늘까지
  cache_dir: "data_cache"
  cache_expire_days: 7          # 캐시 만료일

strategy:
  name: "RSI_MACD"
  rsi_period: 14
  rsi_overbought: 70
  rsi_oversold: 30
  macd_fast: 12
  macd_slow: 26
  macd_signal: 9
  # 매매 신호 강도 (0~1, 1이 가장 강함)
  buy_threshold: 0.6
  sell_threshold: 0.6

portfolio:
  initial_cash: 10000000        # 1천만원 초기 자본
  max_positions: 5              # 최대 보유 종목 수
  commission_rate: 0.00015      # 수수료 (0.015%)
  tax_rate: 0.003               # 거래세 (0.3%)

risk:
  stop_loss_pct: 0.05           # 손절 5%
  take_profit_pct: 0.10         # 익절 10%
  max_daily_loss: 0.03          # 일 최대 손실 3%
  max_portfolio_risk: 0.02      # 포트폴리오 최대 위험 2%

broker:
  order_timeout_seconds: 60     # 주문 타임아웃
  fill_slippage: 0.001          # 슬리피지 (0.1%)
  min_order_amount: 1000        # 최소 주문 금액

trading:
  interval_minutes: 60          # 60분마다 체크
  market_open: "09:00"
  market_close: "15:30"
  holiday_calendar: "KRX"       # 한국 거래소 휴일

logging:
  level: "INFO"                 # DEBUG, INFO, WARNING, ERROR
  file: "logs/trading.log"
  max_bytes: 10485760           # 10MB
  backup_count: 5

visualization:
  output_dir: "results"
  dpi: 150
  figsize: [12, 8]
  style: "seaborn-v0_8"
```

---

## 🔑 `config/.env` (예시 — 실제 사용 시 값을 채워야 함)

```text
# API 키 (현재는 미사용, 향후 확장용)
# UPBIT_ACCESS_KEY=your_access_key
# UPBIT_SECRET_KEY=your_secret_key

# 로깅 비밀번호 (선택)
LOG_PASSWORD=your_log_password
```

---

## 🪵 `src/utils/logger.py`

```python
"""
로깅 설정 모듈
파일과 콘솔에 동시에 로그를 출력합니다.
"""

import logging
import sys
from pathlib import Path
from logging.handlers import RotatingFileHandler
from typing import Optional

import yaml


def setup_logger(
    name: str = "trading_bot",
    log_level: str = "INFO",
    log_file: Optional[str] = "logs/trading.log",
    max_bytes: int = 10 * 1024 * 1024,
    backup_count: int = 5,
) -> logging.Logger:
    """
    전역 로거를 설정합니다.

    Args:
        name: 로거 이름
        log_level: 로그 레벨 (DEBUG, INFO, WARNING, ERROR)
        log_file: 로그 파일 경로 (None이면 콘솔만)
        max_bytes: 로그 파일 최대 크기
        backup_count: 백업 파일 개수

    Returns:
        설정된 로거 인스턴스
    """
    logger = logging.getLogger(name)
    logger.setLevel(getattr(logging, log_level.upper(), logging.INFO))

    # 중복 핸들러 방지
    if logger.handlers:
        return logger

    # 포맷터 생성
    formatter = logging.Formatter(
        fmt="%(asctime)s | %(levelname)-8s | %(name)s | %(message)s",
        datefmt="%Y-%m-%d %H:%M:%S",
    )

    # 콘솔 핸들러
    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setLevel(getattr(logging, log_level.upper(), logging.INFO))
    console_handler.setFormatter(formatter)
    logger.addHandler(console_handler)

    # 파일 핸들러 (옵션)
    if log_file:
        log_path = Path(log_file)
        log_path.parent.mkdir(parents=True, exist_ok=True)
        file_handler = RotatingFileHandler(
            filename=str(log_path),
            maxBytes=max_bytes,
            backupCount=backup_count,
            encoding="utf-8",
        )
        file_handler.setLevel(getattr(logging, log_level.upper(), logging.INFO))
        file_handler.setFormatter(formatter)
        logger.addHandler(file_handler)

    return logger


# 모듈 임포트 시 기본 로거 생성 (settings.yaml을 읽어서 설정)
def initialize_from_config(config_path: str = "config/settings.yaml") -> logging.Logger:
    """
    settings.yaml 파일을 읽어 로거를 초기화합니다.

    Args:
        config_path: 설정 파일 경로

    Returns:
        설정된 로거 인스턴스
    """
    try:
        with open(config_path, "r", encoding="utf-8") as f:
            config = yaml.safe_load(f)
    except FileNotFoundError:
        # 설정 파일이 없으면 기본값으로 로거 설정
        return setup_logger()

    log_config = config.get("logging", {})
    return setup_logger(
        log_level=log_config.get("level", "INFO"),
        log_file=log_config.get("file", "logs/trading.log"),
        max_bytes=log_config.get("max_bytes", 10 * 1024 * 1024),
        backup_count=log_config.get("backup_count", 5),
    )


# 기본 로거 인스턴스 (전역에서 사용)
logger = initialize_from_config()
```

---

## 🛠️ `src/utils/helpers.py`

```python
"""
공통 유틸리티 함수 모음
"""

import os
from datetime import datetime, timedelta
from pathlib import Path
from typing import Any, Dict, Optional

import yaml
from dotenv import load_dotenv


def load_config(config_path: str = "config/settings.yaml") -> Dict[str, Any]:
    """
    YAML 설정 파일을 읽어 딕셔너리로 반환합니다.

    Args:
        config_path: 설정 파일 경로

    Returns:
        설정 딕셔너리

    Raises:
        FileNotFoundError: 설정 파일이 없을 때
        yaml.YAMLError: YAML 파싱 오류
    """
    path = Path(config_path)
    if not path.exists():
        raise FileNotFoundError(f"설정 파일을 찾을 수 없습니다: {config_path}")

    with open(path, "r", encoding="utf-8") as f:
        config = yaml.safe_load(f)

    return config


def load_env(env_path: str = "config/.env") -> None:
    """
    .env 파일을 로드하여 환경 변수로 설정합니다.

    Args:
        env_path: .env 파일 경로
    """
    path = Path(env_path)
    if path.exists():
        load_dotenv(dotenv_path=str(path), verbose=True)


def get_kst_now() -> datetime:
    """
    한국 표준시(KST, UTC+9) 현재 시간을 반환합니다.

    Returns:
        한국 시간 datetime 객체
    """
    from datetime import timezone
    kst = timezone(timedelta(hours=9))
    return datetime.now(kst)


def is_market_open() -> bool:
    """
    한국 주식 시장이 현재 열려 있는지 확인합니다.

    Returns:
        장 중이면 True, 아니면 False
    """
    now = get_kst_now()
    # 평일만 체크 (월~금)
    if now.weekday() >= 5:
        return False
    # 시간 체크: 09:00 ~ 15:30
    market_start = now.replace(hour=9, minute=0, second=0, microsecond=0)
    market_end = now.replace(hour=15, minute=30, second=0, microsecond=0)
    return market_start <= now <= market_end


def ensure_directories() -> None:
    """
    필요한 디렉토리(logs, data_cache, results)가 없으면 생성합니다.
    """
    dirs = ["logs", "data_cache", "results"]
    for d in dirs:
        Path(d).mkdir(parents=True, exist_ok=True)
```

---

## 💾 `src/data/cache.py`

```python
"""
로컬 캐싱 모듈
데이터 수집 결과를 로컬 파일 시스템에 캐싱합니다.
"""

import hashlib
import json
from datetime import datetime, timedelta
from pathlib import Path
from typing import Any, Dict, Optional

import pandas as pd

from src.utils.logger import logger
from src.utils.helpers import load_config


class DataCache:
    """
    주가 데이터를 로컬 CSV 파일로 캐싱하는 클래스
    """

    def __init__(self, config: Optional[Dict[str, Any]] = None):
        """
        캐시 객체 초기화

        Args:
            config: 설정 딕셔너리 (None이면 settings.yaml에서 로드)
        """
        if config is None:
            config = load_config()
        data_config = config.get("data", {})
        self.cache_dir = Path(data_config.get("cache_dir", "data_cache"))
        self.cache_dir.mkdir(parents=True, exist_ok=True)
        self.expire_days = data_config.get("cache_expire_days", 7)
        logger.info(f"캐시 디렉토리: {self.cache_dir}, 만료일: {self.expire_days}일")

    def _make_key(self, ticker: str, start_date: str, end_date: str) -> str:
        """
        종목 코드와 기간을 기반으로 고유 캐시 키를 생성합니다.

        Args:
            ticker: 종목 코드
            start_date: 시작일 (YYYY-MM-DD)
            end_date: 종료일 (YYYY-MM-DD)

        Returns:
            해시 기반 파일명 (확장자 제외)
        """
        raw = f"{ticker}_{start_date}_{end_date}"
        hash_obj = hashlib.md5(raw.encode("utf-8"))
        return hash_obj.hexdigest()

    def get(self, ticker: str, start_date: str, end_date: str) -> Optional[pd.DataFrame]:
        """
        캐시에서 데이터를 조회합니다. 만료되었거나 없으면 None 반환.

        Args:
            ticker: 종목 코드
            start_date: 시작일
            end_date: 종료일

        Returns:
            캐시된 DataFrame 또는 None
        """
        key = self._make_key(ticker, start_date, end_date)
        file_path = self.cache_dir / f"{key}.csv"

        if not file_path.exists():
            logger.debug(f"캐시 미존재: {ticker} ({start_date}~{end_date})")
            return None

        # 파일 수정 시간 확인
        mod_time = datetime.fromtimestamp(file_path.stat().st_mtime)
        if datetime.now() - mod_time > timedelta(days=self.expire_days):
            logger.info(f"캐시 만료: {ticker} ({start_date}~{end_date}), 수정일: {mod_time.date()}")
            file_path.unlink()  # 만료된 파일 삭제
            return None

        try:
            df = pd.read_csv(file_path, index_col=0, parse_dates=True)
            logger.debug(f"캐시 히트: {ticker} ({start_date}~{end_date}), rows={len(df)}")
            return df
        except Exception as e:
            logger.warning(f"캐시 파일 읽기 실패 (삭제 후 재시도): {file_path} - {e}")
            file_path.unlink(missing_ok=True)
            return None

    def set(self, ticker: str, start_date: str, end_date: str, data: pd.DataFrame) -> None:
        """
        데이터를 캐시에 저장합니다.

        Args:
            ticker: 종목 코드
            start_date: 시작일
            end_date: 종료일
            data: 저장할 DataFrame (인덱스가 날짜여야 함)
        """
        key = self._make_key(ticker, start_date, end_date)
        file_path = self.cache_dir / f"{key}.csv"

        try:
            data.to_csv(file_path, encoding="utf-8-sig")
            logger.info(f"캐시 저장 완료: {ticker} ({start_date}~{end_date}), rows={len(data)}")
        except Exception as e:
            logger.error(f"캐시 저장 실패: {file_path} - {e}")

    def clear_expired(self) -> int:
        """
        만료된 모든 캐시 파일을 삭제합니다.

        Returns:
            삭제된 파일 개수
        """
        deleted = 0
        now = datetime.now()
        for file_path in self.cache_dir.glob("*.csv"):
            mod_time = datetime.fromtimestamp(file_path.stat().st_mtime)
            if now - mod_time > timedelta(days=self.expire_days):
                file_path.unlink()
                deleted += 1
                logger.debug(f"만료 캐시 삭제: {file_path}")
        if deleted:
            logger.info(f"만료된 캐시 {deleted}개 삭제 완료")
        return deleted
```

---

## 📡 `src/data/fetcher.py`

```python
"""
주가 데이터 수집 모듈
pykrx와 FinanceDataReader를 이용해 한국 주식 데이터를 가져옵니다.
"""

from datetime import datetime, timedelta
from typing import Dict, List, Optional, Tuple

import pandas as pd
import FinanceDataReader as fdr
from pykrx import stock as krx_stock

from src.utils.logger import logger
from src.utils.helpers import load_config
from src.data.cache import DataCache


class DataFetcher:
    """
    다양한 소스에서 한국 주식 데이터를 수집하는 클래스
    """

    # 거래소 코드 매핑 (pykrx용)
    MARKET_MAP = {
        "KOSPI": "KRX",
        "KOSDAQ": "KRX",
        "KONEX": "KRX",
    }

    def __init__(self, config: Optional[Dict] = None):
        """
        Args:
            config: 설정 딕셔너리. None이면 settings.yaml에서 로드
        """
        if config is None:
            config = load_config()
        self.config = config
        self.data_source = config.get("data", {}).get("source", "pykrx")
        self.cache = DataCache(config)
        logger.info(f"DataFetcher 초기화 완료 (소스: {self.data_source})")

    def fetch_daily_price(
        self,
        ticker: str,
        start_date: str,
        end_date: Optional[str] = None,
        use_cache: bool = True,
    ) -> pd.DataFrame:
        """
        특정 종목의 일별 주가 데이터를 가져옵니다.
        캐시가 있으면 캐시를 우선 사용합니다.

        Args:
            ticker: 종목 코드 (ex: "005930" 삼성전자)
            start_date: 시작일 (YYYY-MM-DD)
            end_date: 종료일 (YYYY-MM-DD, None이면 오늘)
            use_cache: 캐시 사용 여부

        Returns:
            일별 OHLCV 데이터 (인덱스=날짜, 컬럼=Open,High,Low,Close,Volume)

        Raises:
            ValueError: 잘못된 ticker 또는 날짜
            ConnectionError: 데이터 소스 연결 실패
        """
        # end_date 기본값: 오늘
        if end_date is None:
            end_date = datetime.now().strftime("%Y-%m-%d")

        # 입력 검증
        if not ticker or not ticker.isdigit():
            raise ValueError(f"유효하지 않은 종목 코드: {ticker}")

        # 캐시 확인
        if use_cache:
            cached_df = self.cache.get(ticker, start_date, end_date)
            if cached_df is not None and not cached_df.empty:
                logger.info(f"[캐시] {ticker} 데이터 로드 (rows={len(cached_df)})")
                return cached_df

        # 데이터 소스별 수집
        if self.data_source == "pykrx":
            df = self._fetch_from_pykrx(ticker, start_date, end_date)
        elif self.data_source == "finance_datareader":
            df = self._fetch_from_fdr(ticker, start_date, end_date)
        else:
            raise ValueError(f"지원하지 않는 데이터 소스: {self.data_source}")

        # 결과 검증
        if df is None or df.empty:
            logger.warning(f"{ticker} 데이터 없음 ({start_date}~{end_date})")
            return pd.DataFrame()

        # 인덱스 정렬 (오름차순)
        df.sort_index(inplace=True)

        # 캐시 저장
        if use_cache:
            self.cache.set(ticker, start_date, end_date, df)

        logger.info(f"{ticker} 데이터 수집 완료 (rows={len(df)}, source={self.data_source})")
        return df

    def _fetch_from_pykrx(self, ticker: str, start_date: str, end_date: str) -> pd.DataFrame:
        """
        pykrx 라이브러리를 통해 데이터를 가져옵니다.

        Args:
            ticker: 종목 코드
            start_date: 시작일
            end_date: 종료일

        Returns:
            OHLCV DataFrame
        """
        try:
            # pykrx의 get_market_ohlcv_by_date 사용
            df = krx_stock.get_market_ohlcv_by_date(
                fromdate=start_date,
                todate=end_date,
                ticker=ticker,
            )
            if df is not None and not df.empty:
                # 컬럼명 통일 (대문자 시작)
                df.columns = [col.capitalize() for col in df.columns]
                # 거래대금 컬럼 제거 (필요시 유지)
                if "거래대금" in df.columns:
                    df.rename(columns={"거래대금": "Amount"}, inplace=True)
                return df
        except Exception as e:
            logger.error(f"pykrx 데이터 수집 실패 ({ticker}): {e}")
        return pd.DataFrame()

    def _fetch_from_fdr(self, ticker: str, start_date: str, end_date: str) -> pd.DataFrame:
        """
        FinanceDataReader를 통해 데이터를 가져옵니다.

        Args:
            ticker: 종목 코드
            start_date: 시작일
            end_date: 종료일

        Returns:
            OHLCV DataFrame
        """
        try:
            # 한국 주식은 'KS' + 종목코드 형식 사용
            # ex) "005930" -> "KS005930"
            market_code = "KS"  # KOSPI, KOSDAQ 모두 KS 사용 가능
            fdr_ticker = f"{market_code}{ticker}"
            df = fdr.DataReader(fdr_ticker, start=start_date, end=end_date)
            if df is not None and not df.empty:
                # 컬럼명 통일
                df.columns = [col.capitalize() for col in df.columns]
                # 'Change' 컬럼 제거 (있을 경우)
                df.drop(columns=["Change"], inplace=True, errors="ignore")
                return df
        except Exception as e:
            logger.error(f"FinanceDataReader 수집 실패 ({ticker}): {e}")
        return pd.DataFrame()

    def fetch_multiple_tickers(
        self,
        tickers: List[str],
        start_date: str,
        end_date: Optional[str] = None,
        use_cache: bool = True,
    ) -> Dict[str, pd.DataFrame]:
        """
        여러 종목의 데이터를 동시에 가져옵니다.

        Args:
            tickers: 종목 코드 리스트
            start_date: 시작일
            end_date: 종료일
            use_cache: 캐시 사용 여부

        Returns:
            {ticker: DataFrame} 형태의 딕셔너리
        """
        result = {}
        for ticker in tickers:
            try:
                df = self.fetch_daily_price(ticker, start_date, end_date, use_cache)
                if not df.empty:
                    result[ticker] = df
            except Exception as e:
                logger.error(f"{ticker} 데이터 수집 중 오류: {e}")
        logger.info(f"전체 종목 수집 완료: {len(result)}/{len(tickers)} 성공")
        return result

    def get_ticker_list(self, market: str = "KOSPI") -> List[str]:
        """
        특정 시장의 전체 종목 코드 리스트를 반환합니다.

        Args:
            market: 시장 이름 ("KOSPI", "KOSDAQ", "KONEX")

        Returns:
            종목 코드 리스트
        """
        try:
            if self.data_source == "pykrx":
                df = krx_stock.get_market_ticker_list(market=market)
                return df.tolist() if isinstance(df, pd.Series) else list(df)
            else:
                # FinanceDataReader: 전체 종목 리스트는 fdr.StockListing 사용
                df = fdr.StockListing(market)
                return df["Code"].tolist()
        except Exception as e:
            logger.error(f"종목 리스트 조회 실패 ({market}): {e}")
            return []
```

---

## 📊 `src/indicators/technical.py`

```python
"""
기술적 지표 계산 모듈
pandas-ta를 활용하여 다양한 지표를 계산합니다.
"""

from typing import Dict, List, Optional, Union

import pandas as pd
import pandas_ta as ta

from src.utils.logger import logger
from src.utils.helpers import load_config


class TechnicalIndicator:
    """
    기술적 지표 계산 클래스 (RSI, MACD, 볼린저 밴드 등)
    """

    def __init__(self, config: Optional[Dict] = None):
        """
        Args:
            config: 설정 딕셔너리. None이면 settings.yaml에서 로드
        """
        if config is None:
            config = load_config()
        self.config = config
        strategy_config = config.get("strategy", {})
        # RSI 파라미터
        self.rsi_period = strategy_config.get("rsi_period", 14)
        self.rsi_overbought = strategy_config.get("rsi_overbought", 70)
        self.rsi_oversold = strategy_config.get("rsi_oversold", 30)
        # MACD 파라미터
        self.macd_fast = strategy_config.get("macd_fast", 12)
        self.macd_slow = strategy_config.get("macd_slow", 26)
        self.macd_signal = strategy_config.get("macd_signal", 9)

        logger.info(
            f"TechnicalIndicator 초기화: RSI({self.rsi_period}), "
            f"MACD({self.macd_fast},{self.macd_slow},{self.macd_signal})"
        )

    def add_rsi(self, df: pd.DataFrame, column: str = "Close") -> pd.DataFrame:
        """
        RSI(Relative Strength Index) 지표를 추가합니다.

        Args:
            df: OHLCV 데이터프레임
            column: RSI 계산에 사용할 가격 컬럼

        Returns:
            'RSI' 컬럼이 추가된 데이터프레임
        """
        if df.empty or column not in df.columns:
            logger.warning(f"RSI 계산 불가: {column} 컬럼 없음")
            return df

        try:
            # pandas_ta의 rsi 함수 사용
            rsi = ta.rsi(df[column], length=self.rsi_period)
            df["RSI"] = rsi
            logger.debug(f"RSI 계산 완료 (period={self.rsi_period})")
        except Exception as e:
            logger.error(f"RSI 계산 중 오류: {e}")
            df["RSI"] = float("nan")
        return df

    def add_macd(self, df: pd.DataFrame, column: str = "Close") -> pd.DataFrame:
        """
        MACD(Moving Average Convergence Divergence) 지표를 추가합니다.

        Args:
            df: OHLCV 데이터프레임
            column: MACD 계산에 사용할 가격 컬럼

        Returns:
            'MACD', 'MACD_signal', 'MACD_histogram' 컬럼이 추가된 데이터프레임
        """
        if df.empty or column not in df.columns:
            logger.warning(f"MACD 계산 불가: {column} 컬럼 없음")
            return df

        try:
            # pandas_ta의 macd 함수 사용
            macd = ta.macd(
                df[column],
                fast=self.macd_fast,
                slow=self.macd_slow,
                signal=self.macd_signal,
            )
            # macd는 DataFrame을 반환 (컬럼명: MACD_12_26_9 등)
            # 컬럼명 통일을 위해 리네임
            macd.columns = ["MACD", "MACD_signal", "MACD_histogram"]
            df = pd.concat([df, macd], axis=1)
            logger.debug(
                f"MACD 계산 완료 (fast={self.macd_fast}, slow={self.macd_slow}, signal={self.macd_signal})"
            )
        except Exception as e:
            logger.error(f"MACD 계산 중 오류: {e}")
            df["MACD"] = float("nan")
            df["MACD_signal"] = float("nan")
            df["MACD_histogram"] = float("nan")
        return df

    def add_bollinger_bands(
        self, df: pd.DataFrame, column: str = "Close", period: int = 20, std: int = 2
    ) -> pd.DataFrame:
        """
        볼린저 밴드 지표를 추가합니다.

        Args:
            df: OHLCV 데이터프레임
            column: 기준 가격 컬럼
            period: 이동평균 기간
            std: 표준편차 배수

        Returns:
            'BB_upper', 'BB_middle', 'BB_lower' 컬럼이 추가된 데이터프레임
        """
        if df.empty or column not in df.columns:
            logger.warning(f"볼린저 밴드 계산 불가: {column} 컬럼 없음")
            return df

        try:
            bb = ta.bbands(df[column], length=period, std=std)
            bb.columns = ["BB_upper", "BB_middle", "BB_lower"]
            df = pd.concat([df, bb], axis=1)
            logger.debug(f"볼린저 밴드 계산 완료 (period={period}, std={std})")
        except Exception as e:
            logger.error(f"볼린저 밴드 계산 중 오류: {e}")
            df["BB_upper"] = float("nan")
            df["BB_middle"] = float("nan")
            df["BB_lower"] = float("nan")
        return df

    def add_all_indicators(self, df: pd.DataFrame) -> pd.DataFrame:
        """
        모든 주요 지표를 한 번에 추가합니다.

        Args:
            df: OHLCV 데이터프레임

        Returns:
            모든 지표가 추가된 데이터프레임
        """
        df = self.add_rsi(df)
        df = self.add_macd(df)
        df = self.add_bollinger_bands(df)
        return df

    def get_signal_strength(self, df: pd.DataFrame) -> float:
        """
        현재 시점의 매매 신호 강도를 계산합니다 (0~1).
        RSI와 MACD를 기반으로 종합 점수를 산출합니다.

        Args:
            df: 지표가 포함된 최신 데이터프레임 (최소 2행 이상)

        Returns:
            신호 강도 (0: 약한 신호, 1: 강한 신호)
        """
        if df.empty or len(df) < 2:
            return 0.0

        # 최신 데이터
        latest = df.iloc[-1]
        prev = df.iloc[-2]

        score = 0.0
        total_weight = 0.0

        # RSI 신호 (가중치 0.5)
        if "RSI" in latest and not pd.isna(latest["RSI"]):
            rsi_val = latest["RSI"]
            if rsi_val < self.rsi_oversold:
                # 과매도 -> 매수 신호
                score += (self.rsi_oversold - rsi_val) / self.rsi_oversold * 0.5
            elif rsi_val > self.rsi_overbought:
                # 과매수 -> 매도 신호 (음수로 표현)
                score -= (rsi_val - self.rsi_overbought) / (100 - self.rsi_overbought) * 0.5
            total_weight += 0.5

        # MACD 신호 (가중치 0.5)
        if "MACD" in latest and "MACD_signal" in latest:
            macd_val = latest["MACD"]
            signal_val = latest["MACD_signal"]
            prev_macd = prev["MACD"]
            prev_signal = prev["MACD_signal"]

            if not pd.isna(macd_val) and not pd.isna(signal_val):
                # 골든크로스 (MACD가 시그널 위로 돌파) -> 매수
                if prev_macd <= prev_signal and macd_val > signal_val:
                    score += 0.5
                # 데드크로스 (MACD가 시그널 아래로 돌파) -> 매도
                elif prev_macd >= prev_signal and macd_val < signal_val:
                    score -= 0.5
                total_weight += 0.5

        # 가중 평균 (0~1 사이로 정규화)
        if total_weight > 0:
            normalized = score / total_weight
            # -1 ~ 1 범위를 0~1로 변환 (0.5가 중립)
            return (normalized + 1) / 2
        return 0.5
```

---

## 🧱 `src/strategy/base.py`

```python
"""
전략 추상 클래스
모든 매매 전략은 이 클래스를 상속받아 구현합니다.
"""

from abc import ABC, abstractmethod
from datetime import datetime
from typing import Dict, List, Optional, Tuple

import pandas as pd

from src.utils.logger import logger


class TradingSignal:
    """
    매매 신호를 표현하는 데이터 클래스
    """

    def __init__(
        self,
        ticker: str,
        action: str,  # "BUY" 또는 "SELL"
        price: float,
        quantity: int,
        confidence: float = 1.0,
        reason: str = "",
        timestamp: Optional[datetime] = None,
    ):
        """
        Args:
            ticker: 종목 코드
            action: 매매 행동 ("BUY" / "SELL")
            price: 주문 가격
            quantity: 주문 수량
            confidence: 신뢰도 (0~1)
            reason: 신호 발생 이유
            timestamp: 신호 생성 시간 (None이면 현재 시간)
        """
        self.ticker = ticker
        self.action = action.upper()
        if self.action not in ("BUY", "SELL"):
            raise ValueError(f"잘못된 매매 액션: {action}")
        self.price = price
        self.quantity = quantity
        self.confidence = confidence
        self.reason = reason
        self.timestamp = timestamp or datetime.now()

    def __repr__(self) -> str:
        return (
            f"TradingSignal({self.ticker}, {self.action}, "
            f"price={self.price:.0f}, qty={self.quantity}, "
            f"conf={self.confidence:.2f}, reason='{self.reason}')"
        )


class BaseStrategy(ABC):
    """
    모든 매매 전략의 추상 기본 클래스
    """

    def __init__(self, config: Optional[Dict] = None, name: str = "BaseStrategy"):
        """
        Args:
            config: 설정 딕셔너리
            name: 전략 이름
        """
        self.config = config or {}
        self.name = name
        logger.info(f"전략 초기화: {self.name}")

    @abstractmethod
    def generate_signals(
        self,
        data: Dict[str, pd.DataFrame],
        portfolio_state: Dict,
    ) -> List[TradingSignal]:
        """
        주가 데이터와 포트폴리오 상태를 기반으로 매매 신호를 생성합니다.
        모든 하위 클래스는 이 메서드를 구현해야 합니다.

        Args:
            data: {ticker: DataFrame} 형태의 일별 데이터
            portfolio_state: 현재 포트폴리오 상태 (현금, 보유종목 등)

        Returns:
            TradingSignal 객체 리스트
        """
        pass

    def validate_signal(self, signal: TradingSignal) -> bool:
        """
        생성된 신호의 유효성을 검증합니다.

        Args:
            signal: 검증할 신호

        Returns:
            유효하면 True
        """
        if signal.quantity <= 0:
            logger.warning(f"유효하지 않은 수량: {signal}")
            return False
        if signal.price <= 0:
            logger.warning(f"유효하지 않은 가격: {signal}")
            return False
        if signal.confidence < 0 or signal.confidence > 1:
            logger.warning(f"유효하지 않은 신뢰도: {signal}")
            return False
        return True
```

---

## 📈 `src/strategy/rsi_macd.py`

```python
"""
RSI + MACD 복합 전략 구현
두 지표를 조합하여 매매 신호를 생성합니다.
"""

from typing import Dict, List, Optional

import pandas as pd

from src.strategy.base import BaseStrategy, TradingSignal
from src.indicators.technical import TechnicalIndicator
from src.utils.logger import logger
from src.utils.helpers import load_config


class RSIMACDStrategy(BaseStrategy):
    """
    RSI와 MACD를 결합한 매매 전략

    규칙:
    - RSI가 과매도 구간(<30)이고 MACD가 골든크로스 → 강한 매수
    - RSI가 과매수 구간(>70)이고 MACD가 데드크로스 → 강한 매도
    - RSI 단독 과매도/과매수도 신호로 사용 (신뢰도 낮음)
    """

    def __init__(self, config: Optional[Dict] = None):
        """
        Args:
            config: 설정 딕셔너리. None이면 settings.yaml 로드
        """
        if config is None:
            config = load_config()
        super().__init__(config, name="RSI_MACD")
        self.indicator = TechnicalIndicator(config)
        # 설정에서 매매 임계값 읽기
        strategy_config = config.get("strategy", {})
        self.buy_threshold = strategy_config.get("buy_threshold", 0.6)
        self.sell_threshold = strategy_config.get("sell_threshold", 0.6)

    def generate_signals(
        self,
        data: Dict[str, pd.DataFrame],
        portfolio_state: Dict,
    ) -> List[TradingSignal]:
        """
        RSI + MACD 기반 매매 신호 생성

        Args:
            data: {ticker: DataFrame} 형태의 가격 데이터
            portfolio_state: 포트폴리오 상태 딕셔너리
                - "cash": float
                - "holdings": {ticker: {"quantity": int, "avg_price": float}}

        Returns:
            유효한 TradingSignal 리스트
        """
        signals: List[TradingSignal] = []
        cash = portfolio_state.get("cash", 0)
        holdings = portfolio_state.get("holdings", {})

        for ticker, df in data.items():
            if df is None or df.empty:
                continue

            # 기술적 지표 계산
            df = self.indicator.add_all_indicators(df)

            # 최신 데이터가 충분한지 확인 (최소 RSI 기간 이상)
            if len(df) < max(self.indicator.rsi_period, self.indicator.macd_slow):
                logger.debug(f"{ticker}: 데이터 부족으로 스킵 ({len(df)}행)")
                continue

            # 현재 가격 (마지막 종가)
            latest = df.iloc[-1]
            current_price = latest.get("Close")
            if pd.isna(current_price) or current_price == 0:
                continue

            # 신호 강도 계산
            signal_strength = self.indicator.get_signal_strength(df)

            # 현재 보유 여부
            is_holding = ticker in holdings and holdings[ticker].get("quantity", 0) > 0

            # 매수 신호
            if not is_holding and signal_strength >= self.buy_threshold:
                # 매수 수량 결정: 전체 현금의 일정 비율 (여기서는 20%로 고정, settings에서 관리 가능)
                buy_amount = cash * 0.2  # TODO: settings.yaml에서 읽도록 개선
                if buy_amount < 1000:
                    continue  # 최소 주문 금액 미만
                quantity = int(buy_amount / current_price)
                if quantity < 1:
                    continue
                # 10주 단위로 라운딩 (필요시)
                quantity = (quantity // 10) * 10
                if quantity == 0:
                    continue

                reason = f"RSI_MACD 신호: 강도={signal_strength:.2f}"
                signal = TradingSignal(
                    ticker=ticker,
                    action="BUY",
                    price=current_price,
                    quantity=quantity,
                    confidence=signal_strength,
                    reason=reason,
                )
                signals.append(signal)
                logger.info(f"매수 신호 생성: {signal}")

            # 매도 신호
            elif is_holding and signal_strength <= (1 - self.sell_threshold):
                # 전량 매도
                quantity = holdings[ticker]["quantity"]
                if quantity <= 0:
                    continue

                reason = f"RSI_MACD 신호: 강도={signal_strength:.2f}"
                signal = TradingSignal(
                    ticker=ticker,
                    action="SELL",
                    price=current_price,
                    quantity=quantity,
                    confidence=1 - signal_strength,
                    reason=reason,
                )
                signals.append(signal)
                logger.info(f"매도 신호 생성: {signal}")

        return signals
```

---

## 🏦 `src/broker/paper_broker.py`

```python
"""
가상 주문 처리 브로커 (Paper Broker)
실제 주문을 체결하지 않고 가상으로 체결 로그를 남깁니다.
"""

from datetime import datetime
from typing import Dict, List, Optional, Tuple
from enum import Enum

import pandas as pd

from src.utils.logger import logger
from src.utils.helpers import load_config


class OrderType(Enum):
    """주문 유형"""
    MARKET = "MARKET"   # 시장가
    LIMIT = "LIMIT"     # 지정가


class OrderStatus(Enum):
    """주문 상태"""
    PENDING = "PENDING"
    FILLED = "FILLED"
    PARTIALLY_FILLED = "PARTIALLY_FILLED"
    CANCELLED = "CANCELLED"
    REJECTED = "REJECTED"


class Order:
    """
    개별 주문 정보를 저장하는 데이터 클래스
    """

    def __init__(
        self,
        ticker: str,
        order_type: OrderType,
        side: str,  # "BUY" or "SELL"
        quantity: int,
        price: float,
        order_id: Optional[str] = None,
        timestamp: Optional[datetime] = None,
    ):
        """
        Args:
            ticker: 종목 코드
            order_type: 주문 유형 (MARKET/LIMIT)
            side: 매수/매도
            quantity: 주문 수량
            price: 주문 가격
            order_id: 주문 ID (None이면 자동 생성)
            timestamp: 주문 시간
        """
        self.ticker = ticker
        self.order_type = order_type
        self.side = side.upper()
        if self.side not in ("BUY", "SELL"):
            raise ValueError(f"잘못된 주문 방향: {side}")
        self.quantity = quantity
        self.price = price
        self.order_id = order_id or f"ORDER_{datetime.now().strftime('%Y%m%d%H%M%S%f')}"
        self.timestamp = timestamp or datetime.now()
        self.status = OrderStatus.PENDING
        self.filled_quantity = 0
        self.filled_price = 0.0
        self.commission = 0.0
        self.tax = 0.0

    def __repr__(self) -> str:
        return (
            f"Order({self.order_id}, {self.ticker}, {self.side}, "
            f"qty={self.quantity}, price={self.price:.0f}, "
            f"status={self.status.value})"
        )


class PaperBroker:
    """
    가상 거래를 처리하는 브로커
    실제 체결은 발생하지 않으며, 주문 접수와 체결을 시뮬레이션합니다.
    """

    def __init__(self, config: Optional[Dict] = None):
        """
        Args:
            config: 설정 딕셔너리. None이면 settings.yaml 로드
        """
        if config is None:
            config = load_config()
        self.config = config
        broker_config = config.get("broker", {})
        self.commission_rate = config.get("portfolio", {}).get("commission_rate", 0.00015)
        self.tax_rate = config.get("portfolio", {}).get("tax_rate", 0.003)
        self.min_order_amount = broker_config.get("min_order_amount", 1000)
        self.fill_slippage = broker_config.get("fill_slippage", 0.001)
        self.order_timeout = broker_config.get("order_timeout_seconds", 60)

        # 주문 기록 저장 (모든 주문)
        self.orders: List[Order] = []
        logger.info(f"PaperBroker 초기화 완료 (수수료: {self.commission_rate*100:.3f}%)")

    def place_order(
        self,
        ticker: str,
        side: str,
        quantity: int,
        price: float,
        order_type: OrderType = OrderType.MARKET,
        current_market_price: Optional[float] = None,
    ) -> Order:
        """
        가상 주문을 접수하고 체결까지 시뮬레이션합니다.

        Args:
            ticker: 종목 코드
            side: "BUY" 또는 "SELL"
            quantity: 주문 수량
            price: 지정 가격 (MARKET 주문이면 현재가로 간주)
            order_type: 주문 유형
            current_market_price: 현재 시장 가격 (None이면 price 사용)

        Returns:
            체결된 Order 객체

        Raises:
            ValueError: 잘못된 파라미터
        """
        # 기본 검증
        if quantity <= 0:
            raise ValueError(f"수량은 0보다 커야 합니다: {quantity}")
        if price <= 0:
            raise ValueError(f"가격은 0보다 커야 합니다: {price}")
        if side not in ("BUY", "SELL"):
            raise ValueError(f"유효하지 않은 주문 방향: {side}")

        # 주문 생성
        order = Order(
            ticker=ticker,
            order_type=order_type,
            side=side,
            quantity=quantity,
            price=price,
        )

        # 체결 가격 결정 (슬리피지 적용)
        if order_type == OrderType.MARKET:
            # 시장가 주문: 현재 시장가 사용
            fill_price = current_market_price if current_market_price else price
            # 슬리피지 적용 (매수는 +, 매도는 -)
            slippage = fill_price * self.fill_slippage
            if side == "BUY":
                fill_price += slippage
            else:
                fill_price -= slippage
        else:
            # 지정가 주문: price 사용 (슬리피지 없음)
            fill_price = price

        # 최소 주문 금액 확인
        if fill_price * quantity < self.min_order_amount:
            order.status = OrderStatus.REJECTED
            logger.warning(f"최소 주문 금액 미만으로 주문 거부: {order}")
            self.orders.append(order)
            return order

        # 수수료와 세금 계산
        commission = fill_price * quantity * self.commission_rate
        tax = 0.0
        if side == "SELL":
            tax = fill_price * quantity * self.tax_rate  # 매도 시 거래세

        # 체결 처리
        order.status = OrderStatus.FILLED
        order.filled_quantity = quantity
        order.filled_price = round(fill_price, 2)
        order.commission = round(commission, 2)
        order.tax = round(tax, 2)
        order.timestamp = datetime.now()

        self.orders.append(order)
        logger.info(
            f"주문 체결 완료: {order.side} {order.ticker} "
            f"{order.filled_quantity}주 @ {order.filled_price:.0f}원 "
            f"(수수료={order.commission:.0f}, 세금={order.tax:.0f})"
        )
        return order

    def cancel_order(self, order_id: str) -> bool:
        """
        주문을 취소합니다. (이미 체결된 주문은 취소 불가)

        Args:
            order_id: 취소할 주문 ID

        Returns:
            취소 성공 시 True
        """
        for order in self.orders:
            if order.order_id == order_id:
                if order.status in (OrderStatus.PENDING, OrderStatus.PARTIALLY_FILLED):
                    order.status = OrderStatus.CANCELLED
                    logger.info(f"주문 취소됨: {order}")
                    return True
                else:
                    logger.warning(f"이미 체결된 주문은 취소 불가: {order}")
                    return False
        logger.warning(f"주문 ID를 찾을 수 없음: {order_id}")
        return False

    def get_order_history(self, ticker: Optional[str] = None) -> List[Order]:
        """
        주문 내역을 조회합니다.

        Args:
            ticker: 특정 종목만 필터링 (None이면 전체)

        Returns:
            Order 객체 리스트
        """
        if ticker:
            return [o for o in self.orders if o.ticker == ticker]
        return self.orders.copy()

    def get_daily_summary(self, date: Optional[str] = None) -> Dict:
        """
        특정 일자의 주문 요약 정보를 반환합니다.

        Args:
            date: 날짜 문자열 (YYYY-MM-DD). None이면 오늘

        Returns:
            요약 딕셔너리
        """
        from datetime import date as date_type
        target_date = date if date else str(date_type.today())

        daily_orders = [
            o for o in self.orders
            if o.timestamp.strftime("%Y-%m-%d") == target_date
        ]

        total_buy = sum(
            o.filled_price * o.filled_quantity
            for o in daily_orders if o.side == "BUY" and o.status == OrderStatus.FILLED
        )
        total_sell = sum(
            o.filled_price * o.filled_quantity
            for o in daily_orders if o.side == "SELL" and o.status == OrderStatus.FILLED
        )
        total_commission = sum(o.commission for o in daily_orders)
        total_tax = sum(o.tax for o in daily_orders)

        return {
            "date": target_date,
            "total_orders": len(daily_orders),
            "filled_orders": sum(1 for o in daily_orders if o.status == OrderStatus.FILLED),
            "total_buy_amount": round(total_buy, 2),
            "total_sell_amount": round(total_sell, 2),
            "total_commission": round(total_commission, 2),
            "total_tax": round(total_tax, 2),
            "net_amount": round(total_sell - total_buy - total_commission - total_tax, 2),
        }
```

---

## 💼 `src/portfolio/manager.py`

```python
"""
포트폴리오 상태 관리 모듈
현금, 보유 종목, 손익 등을 관리합니다.
"""

from datetime import datetime
from typing import Dict, List, Optional, Tuple

import pandas as pd

from src.utils.logger import logger
from src.utils.helpers import load_config
from src.broker.paper_broker import PaperBroker, Order, OrderStatus


class PortfolioManager:
    """
    가상 포트폴리오의 상태를 관리하는 클래스
    """

    def __init__(
        self,
        config: Optional[Dict] = None,
        broker: Optional[PaperBroker] = None,
    ):
        """
        Args:
            config: 설정 딕셔너리. None이면 settings.yaml 로드
            broker: PaperBroker 인스턴스 (None이면 새로 생성)
        """
        if config is None:
            config = load_config()
        self.config = config
        portfolio_config = config.get("portfolio", {})
        self.initial_cash = portfolio_config.get("initial_cash", 10_000_000)
        self.cash = self.initial_cash
        # 보유 종목: {ticker: {"quantity": int, "avg_price": float, "total_cost": float}}
        self.holdings: Dict[str, Dict] = {}
        self.broker = broker or PaperBroker(config)
        # 거래 내역 (히스토리)
        self.trade_history: List[Dict] = []
        logger.info(
            f"PortfolioManager 초기화: 초기 자본={self.initial_cash:,.0f}원"
        )

    def get_state(self) -> Dict:
        """
        현재 포트폴리오 상태를 반환합니다.

        Returns:
            포트폴리오 상태 딕셔너리
            {
                "cash": float,
                "holdings": {ticker: {"quantity": int, "avg_price": float}},
                "total_value": float,
                "total_invested": float,
                "unrealized_pnl": float,
                "realized_pnl": float,
            }
        """
        total_invested = sum(
            h["total_cost"] for h in self.holdings.values()
        )
        # 평가 금액은 현재가를 알 수 없으므로 평균 매입가로 계산 (실시간 업데이트 필요)
        current_value = sum(
            h["quantity"] * h["avg_price"] for h in self.holdings.values()
        )
        unrealized_pnl = current_value - total_invested

        return {
            "cash": self.cash,
            "holdings": {
                ticker: {
                    "quantity": info["quantity"],
                    "avg_price": info["avg_price"],
                }
                for ticker, info in self.holdings.items()
            },
            "total_value": self.cash + current_value,
            "total_invested": total_invested,
            "unrealized_pnl": round(unrealized_pnl, 2),
            "realized_pnl": self._calculate_realized_pnl(),
        }

    def _calculate_realized_pnl(self) -> float:
        """
        지금까지 실현된 손익을 계산합니다.

        Returns:
            실현 손익 합계
        """
        realized = 0.0
        for trade in self.trade_history:
            if trade["side"] == "SELL":
                realized += trade.get("pnl", 0)
        return round(realized, 2)

    def execute_order(self, order: Order, current_prices: Dict[str, float]) -> bool:
        """
        주문을 실행하여 포트폴리오를 업데이트합니다.

        Args:
            order: 체결된 Order 객체
            current_prices: {ticker: 현재가} 딕셔너리 (매수/매도 가격 결정용)

        Returns:
            실행 성공 시 True
        """
        if order.status != OrderStatus.FILLED:
            logger.warning(f"체결되지 않은 주문은 실행할 수 없음: {order.status}")
            return False

        ticker = order.ticker
        side = order.side
        quantity = order.filled_quantity
        price = order.filled_price
        commission = order.commission
        tax = order.tax

        if side == "BUY":
            # 매수: 충분한 현금 확인
            total_cost = price * quantity + commission
            if total_cost > self.cash:
                logger.warning(f"현금 부족으로 매수 불가: 필요={total_cost:,.0f}, 보유={self.cash:,.0f}")
                return False

            # 보유 종목 업데이트
            if ticker in self.holdings:
                # 이미 보유 중이면 평균 단가 갱신
                existing = self.holdings[ticker]
                total_qty = existing["quantity"] + quantity
                total_cost_existing = existing["total_cost"]
                total_cost_new = price * quantity + commission
                new_avg_price = (total_cost_existing + total_cost_new) / total_qty
                self.holdings[ticker] = {
                    "quantity": total_qty,
                    "avg_price": round(new_avg_price, 2),
                    "total_cost": total_cost_existing + total_cost_new,
                }
            else:
                self.holdings[ticker] = {
                    "quantity": quantity,
                    "avg_price": round(price, 2),
                    "total_cost": price * quantity + commission,
                }

            # 현금 차감
            self.cash -= total_cost
            logger.info(
                f"매수 실행: {ticker} {quantity}주 @ {price:,.0f}원 "
                f"(총비용={total_cost:,.0f}원, 현금={self.cash:,.0f}원)"
            )

        elif side == "SELL":
            # 매도: 보유 수량 확인
            if ticker not in self.holdings:
                logger.warning(f"보유하지 않은 종목 매도 시도: {ticker}")
                return False
            if self.holdings[ticker]["quantity"] < quantity:
                logger.warning(f"보유 수량 부족: 필요={quantity}, 보유={self.holdings[ticker]['quantity']}")
                return False

            # 손익 계산
            cost_basis = self.holdings[ticker]["avg_price"]
            total_revenue = price * quantity - commission - tax
            pnl = total_revenue - cost_basis * quantity

            # 보유 종목 업데이트
            existing = self.holdings[ticker]
            remaining_qty = existing["quantity"] - quantity
            if remaining_qty == 0:
                del self.holdings[ticker]
            else:
                # 남은 수량의 총 비용 비례 배분
                remaining_cost = existing["total_cost"] * (remaining_qty / existing["quantity"])
                self.holdings[ticker] = {
                    "quantity": remaining_qty,
                    "avg_price": existing["avg_price"],
                    "total_cost": remaining_cost,
                }

            # 현금 증가
            self.cash += total_revenue
            logger.info(
                f"매도 실행: {ticker} {quantity}주 @ {price:,.0f}원 "
                f"(수익={total_revenue:,.0f}원, 손익={pnl:,.0f}원, 현금={self.cash:,.0f}원)"
            )

            # 거래 내역 저장
            trade_record = {
                "timestamp": order.timestamp,
                "ticker": ticker,
                "side": "SELL",
                "quantity": quantity,
                "price": price,
                "cost_basis": cost_basis,
                "pnl": round(pnl, 2),
                "commission": commission,
                "tax": tax,
            }
            self.trade_history.append(trade_record)

        else:
            logger.error(f"알 수 없는 주문 방향: {side}")
            return False

        return True

    def update_prices(self, current_prices: Dict[str, float]) -> None:
        """
        현재 가격으로 포트폴리오 평가액을 업데이트합니다.
        (실제로는 평가손익 계산용)

        Args:
            current_prices: {ticker: 현재가}
        """
        pass

    def get_return_rate(self) -> float:
        """
        초기 자본 대비 총 수익률을 반환합니다.

        Returns:
            수익률 (0.1 = 10%)
        """
        state = self.get_state()
        total_value = state["total_value"]
        return (total_value - self.initial_cash) / self.initial_cash

    def reset(self) -> None:
        """
        포트폴리오를 초기 상태로 리셋합니다.
        """
        self.cash = self.initial_cash
        self.holdings.clear()
        self.trade_history.clear()
        self.broker = PaperBroker(self.config)
        logger.info("포트폴리오가 초기화되었습니다.")
```

---

## 🛡️ `src/risk/manager.py`

```python
"""
리스크 관리 모듈
손절/익절, 일 최대 손실, 포트폴리오 위험도 등을 관리합니다.
"""

from datetime import datetime, date
from typing import Dict, List, Optional, Tuple

import pandas as pd

from src.utils.logger import logger
from src.utils.helpers import load_config
from src.portfolio.manager import PortfolioManager
from src.strategy.base import TradingSignal


class RiskManager:
    """
    리스크 관리를 담당하는 클래스
    손절/익절, 일일 손실 한도, 포트폴리오 분산 등을 체크합니다.
    """

    def __init__(self, config: Optional[Dict] = None):
        """
        Args:
            config: 설정 딕셔너리. None이면 settings.yaml 로드
        """
        if config is None:
            config = load_config()
        self.config = config
        risk_config = config.get("risk", {})
        self.stop_loss_pct = risk_config.get("stop_loss_pct", 0.05)      # 5%
        self.take_profit_pct = risk_config.get("take_profit_pct", 0.10)  # 10%
        self.max_daily_loss = risk_config.get("max_daily_loss", 0.03)    # 3%
        self.max_portfolio_risk = risk_config.get("max_portfolio_risk", 0.02)  # 2%
        # 일일 손실 추적
        self.daily_loss: Dict[str, float] = {}  # {date_str: cumulative_loss}
        logger.info(
            f"RiskManager 초기화: 손절={self.stop_loss_pct*100:.0f}%, "
            f"익절={self.take_profit_pct*100:.0f}%, "
            f"일최대손실={self.max_daily_loss*100:.0f}%"
        )

    def check_stop_loss(
        self,
        ticker: str,
        current_price: float,
        avg_price: float,
    ) -> bool:
        """
        손절 조건을 체크합니다. (현재가가 평균 단가 대비 손절선 아래인지)

        Args:
            ticker: 종목 코드
            current_price: 현재 가격
            avg_price: 평균 매입 단가

        Returns:
            손절 필요하면 True (매도 신호)
        """
        if avg_price <= 0:
            return False
        loss_pct = (avg_price - current_price) / avg_price
        if loss_pct >= self.stop_loss_pct:
            logger.warning(
                f"손절 조건 도달: {ticker} "
                f"평균={avg_price:,.0f}, 현재={current_price:,.0f}, "
                f"손실률={loss_pct*100:.2f}% (기준={self.stop_loss_pct*100:.0f}%)"
            )
            return True
        return False

    def check_take_profit(
        self,
        ticker: str,
        current_price: float,
        avg_price: float,
    ) -> bool:
        """
        익절 조건을 체크합니다. (현재가가 평균 단가 대비 익절선 위인지)

        Args:
            ticker: 종목 코드
            current_price: 현재 가격
            avg_price: 평균 매입 단가

        Returns:
            익절 필요하면 True (매도 신호)
        """
        if avg_price <= 0:
            return False
        profit_pct = (current_price - avg_price) / avg_price
        if profit_pct >= self.take_profit_pct:
            logger.info(
                f"익절 조건 도달: {ticker} "
                f"평균={avg_price:,.0f}, 현재={current_price:,.0f}, "
                f"수익률={profit_pct*100:.2f}% (기준={self.take_profit_pct*100:.0f}%)"
            )
            return True
        return False

    def check_daily_loss_limit(
        self,
        portfolio: PortfolioManager,
        current_prices: Dict[str, float],
    ) -> bool:
        """
        일일 최대 손실 한도를 초과했는지 확인합니다.

        Args:
            portfolio: 포트폴리오 매니저
            current_prices: {ticker: 현재가}

        Returns:
            일일 손실 한도 초과 시 True (거래 중단 권고)
        """
        state = portfolio.get_state()
        total_value = state["total_value"]
        initial = portfolio.initial_cash
        total_return = (total_value - initial) / initial

        today = str(date.today())
        if today not in self.daily_loss:
            self.daily_loss[today] = 0.0

        # 오늘 손실률 업데이트 (음수면 손실)
        if total_return < 0:
            self.daily_loss[today] = min(self.daily_loss[today], total_return)

        if abs(self.daily_loss[today]) >= self.max_daily_loss:
            logger.critical(
                f"일일 최대 손실 한도 초과! "
                f"오늘 손실률={self.daily_loss[today]*100:.2f}% "
                f"(한도={self.max_daily_loss*100:.0f}%)"
            )
            return True
        return False

    def check_portfolio_risk(
        self,
        portfolio: PortfolioManager,
        current_prices: Dict[str, float],
    ) -> bool:
        """
        포트폴리오 전체 위험도를 체크합니다.

        Args:
            portfolio: 포트폴리오 매니저
            current_prices: {ticker: 현재가}

        Returns:
            위험 한도 초과 시 True
        """
        state = portfolio.get_state()
        total_value = state["total_value"]
        if total_value == 0:
            return False

        holdings = state["holdings"]
        for ticker, info in holdings.items():
            current_price = current_prices.get(ticker, info["avg_price"])
            position_value = info["quantity"] * current_price
            weight = position_value / total_value
            if weight > self.max_portfolio_risk:
                logger.warning(
                    f"포트폴리오 위험 초과: {ticker} "
                    f"비중={weight*100:.2f}% (한도={self.max_portfolio_risk*100:.0f}%)"
                )
                return True
        return False

    def apply_risk_filters(
        self,
        signals: List[TradingSignal],
        portfolio: PortfolioManager,
        current_prices: Dict[str, float],
    ) -> List[TradingSignal]:
        """
        생성된 매매 신호에 리스크 필터를 적용합니다.

        Args:
            signals: 원본 매매 신호 리스트
            portfolio: 포트폴리오 매니저
            current_prices: {ticker: 현재가}

        Returns:
            리스크 필터를 통과한 신호 리스트
        """
        filtered_signals = []

        # 일일 손실 한도 체크
        if self.check_daily_loss_limit(portfolio, current_prices):
            logger.warning("일일 손실 한도 초과로 모든 신호 차단")
            return []

        for signal in signals:
            ticker = signal.ticker
            side = signal.side

            # 매도 신호는 항상 허용 (손절/익절 포함)
            if side == "SELL":
                filtered_signals.append(signal)
                continue

            # 매수 신호에 대한 리스크 체크
            if self.check_portfolio_risk(portfolio, current_prices):
                logger.warning(f"포트폴리오 위험 초과로 매수 신호 차단: {ticker}")
                continue

            filtered_signals.append(signal)

        return filtered_signals
```

---

## 📉 `src/reporter/visualizer.py`

```python
"""
결과 시각화 모듈
포트폴리오 성과, 거래 내역, 지표 등을 그래프로 표현합니다.
"""

from pathlib import Path
from typing import Dict, List, Optional, Tuple

import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
import plotly.graph_objects as go
from plotly.subplots import make_subplots

from src.utils.logger import logger
from src.utils.helpers import load_config
from src.portfolio.manager import PortfolioManager
from src.broker.paper_broker import Order, OrderStatus, OrderType


class Visualizer:
    """
    트레이딩 결과를 시각화하는 클래스
    matplotlib과 plotly를 지원합니다.
    """

    def __init__(self, config: Optional[Dict] = None):
        if config is None:
            config = load_config()
        self.config = config
        viz_config = config.get("visualization", {})
        self.output_dir = Path(viz_config.get("output_dir", "results"))
        self.output_dir.mkdir(parents=True, exist_ok=True)
        self.dpi = viz_config.get("dpi", 150)
        self.figsize = tuple(viz_config.get("figsize", [12, 8]))
        self.style = viz_config.get("style", "seaborn-v0_8")
        plt.style.use(self.style)
        logger.info(f"Visualizer 초기화: 출력 디렉토리={self.output_dir}")

    def plot_portfolio_performance(
        self,
        portfolio_history: pd.DataFrame,
        title: str = "포트폴리오 성과",
        save: bool = True,
        show: bool = False,
    ) -> Optional[str]:
        if portfolio_history.empty:
            logger.warning("포트폴리오 데이터가 비어있어 그래프를 생성할 수 없습니다.")
            return None

        fig, ax = plt.subplots(figsize=self.figsize)
        ax.plot(
            portfolio_history.index,
            portfolio_history["total_value"],
            label="총 자산",
            color="blue",
            linewidth=2,
        )
        if "cash" in portfolio_history.columns:
            ax.fill_between(
                portfolio_history.index,
                portfolio_history["cash"],
                portfolio_history["total_value"],
                alpha=0.2,
                color="gray",
                label="투자 금액",
            )

        ax.set_title(title, fontsize=14)
        ax.set_xlabel("날짜")
        ax.set_ylabel("가치 (원)")
        ax.legend()
        ax.grid(True, alpha=0.3)
        ax.xaxis.set_major_formatter(mdates.DateFormatter("%Y-%m-%d"))
        fig.autofmt_xdate()

        if save:
            file_path = self.output_dir / "portfolio_performance.png"
            fig.savefig(file_path, dpi=self.dpi, bbox_inches="tight")
            logger.info(f"포트폴리오 성과 그래프 저장: {file_path}")

        if show:
            plt.show()
        else:
            plt.close(fig)

        return str(file_path) if save else None

    def plot_equity_curve(
        self,
        portfolio_history: pd.DataFrame,
        title: str = "자본 곡선",
        save: bool = True,
        show: bool = False,
    ) -> Optional[str]:
        if portfolio_history.empty:
            return None

        cumulative = portfolio_history["total_value"]
        running_max = cumulative.cummax()
        drawdown = (cumulative - running_max) / running_max * 100

        fig, (ax1, ax2) = plt.subplots(
            2, 1, figsize=(self.figsize[0], self.figsize[1] * 1.2), sharex=True
        )
        ax1.plot(cumulative.index, cumulative, label="총 자산", color="green", linewidth=2)
        ax1.set_title(title, fontsize=14)
        ax1.set_ylabel("자본 (원)")
        ax1.legend()
        ax1.grid(True, alpha=0.3)

        ax2.fill_between(drawdown.index, 0, drawdown, color="red", alpha=0.5, label="Drawdown")
        ax2.set_ylabel("Drawdown (%)")
        ax2.set_xlabel("날짜")
        ax2.legend()
        ax2.grid(True, alpha=0.3)
        ax2.xaxis.set_major_formatter(mdates.DateFormatter("%Y-%m-%d"))
        fig.autofmt_xdate()

        if save:
            file_path = self.output_dir / "equity_curve.png"
            fig.savefig(file_path, dpi=self.dpi, bbox_inches="tight")
            logger.info(f"자본 곡선 그래프 저장: {file_path}")

        if show:
            plt.show()
        else:
            plt.close(fig)

        return str(file_path) if save else None

    def plot_trade_signals(
        self,
        price_data: pd.DataFrame,
        orders: List[Order],
        ticker: str,
        title: Optional[str] = None,
        save: bool = True,
        show: bool = False,
    ) -> Optional[str]:
        if price_data.empty:
            logger.warning(f"{ticker} 가격 데이터 없음")
            return None

        fig = make_subplots(
            rows=2, cols=1,
            shared_xaxes=True,
            vertical_spacing=0.03,
            row_heights=[0.7, 0.3],
            subplot_titles=(f"{ticker} 주가", "RSI (선택적)"),
        )

        fig.add_trace(
            go.Candlestick(
                x=price_data.index,
                open=price_data["Open"],
                high=price_data["High"],
                low=price_data["Low"],
                close=price_data["Close"],
                name="가격",
            ),
            row=1, col=1,
        )

        buy_orders = [o for o in orders if o.side == "BUY" and o.status == OrderStatus.FILLED]
        sell_orders = [o for o in orders if o.side == "SELL" and o.status == OrderStatus.FILLED]

        if buy_orders:
            fig.add_trace(
                go.Scatter(
                    x=[o.timestamp for o in buy_orders],
                    y=[o.filled_price for o in buy_orders],
                    mode="markers",
                    marker=dict(symbol="triangle-up", size=12, color="green"),
                    name="매수",
                ),
                row=1, col=1,
            )
        if sell_orders:
            fig.add_trace(
                go.Scatter(
                    x=[o.timestamp for o in sell_orders],
                    y=[o.filled_price for o in sell_orders],
                    mode="markers",
                    marker=dict(symbol="triangle-down", size=12, color="red"),
                    name="매도",
                ),
                row=1, col=1,
            )

        if "RSI" in price_data.columns:
            fig.add_trace(
                go.Scatter(
                    x=price_data.index,
                    y=price_data["RSI"],
                    mode="lines",
                    name="RSI",
                    line=dict(color="purple", width=1),
                ),
                row=2, col=1,
            )
            fig.add_hline(y=70, line_dash="dash", line_color="red", row=2, col=1)
            fig.add_hline(y=30, line_dash="dash", line_color="green", row=2, col=1)
            fig.update_yaxes(range=[0, 100], row=2, col=1)

        fig.update_layout(
            title=title or f"{ticker} 거래 신호",
            xaxis_title="날짜",
            yaxis_title="가격 (원)",
            xaxis_rangeslider_visible=False,
            height=800,
            showlegend=True,
        )

        if save:
            file_path = self.output_dir / f"{ticker}_trade_signals.html"
            fig.write_html(str(file_path))
            logger.info(f"거래 신호 차트 저장: {file_path}")

        return str(file_path) if save else None

    def generate_report(
        self,
        portfolio: PortfolioManager,
        price_data: Dict[str, pd.DataFrame],
    ) -> None:
        logger.info("리포트 생성 시작...")
        state = portfolio.get_state()
        orders = portfolio.broker.get_order_history()

        dummy_history = pd.DataFrame(
            {"total_value": [state["total_value"]]},
            index=[pd.Timestamp.now()],
        )
        self.plot_portfolio_performance(dummy_history, save=True, show=False)

        for ticker in state["holdings"]:
            if ticker in price_data:
                ticker_orders = [o for o in orders if o.ticker == ticker]
                self.plot_trade_signals(
                    price_data[ticker], ticker_orders, ticker, save=True, show=False
                )

        logger.info("리포트 생성 완료")
```

---

## 🚀 `main.py`

```python
"""
가상 트레이딩 봇 메인 진입점
실시간 또는 스케줄 기반으로 매매를 실행합니다.
"""

import sys
from pathlib import Path
from datetime import datetime, time
from typing import Dict, List, Optional

import schedule
import time as time_module
import pandas as pd

sys.path.insert(0, str(Path(__file__).parent))

from src.utils.logger import logger, initialize_from_config
from src.utils.helpers import load_config, load_env, ensure_directories, is_market_open, get_kst_now
from src.data.fetcher import DataFetcher
from src.indicators.technical import TechnicalIndicator
from src.strategy.rsi_macd import RSIMACDStrategy
from src.broker.paper_broker import PaperBroker, OrderType
from src.portfolio.manager import PortfolioManager
from src.risk.manager import RiskManager
from src.reporter.visualizer import Visualizer


class TradingBot:
    """
    가상 트레이딩 봇 메인 클래스
    모든 모듈을 통합하여 매매 사이클을 관리합니다.
    """

    def __init__(self, config_path: str = "config/settings.yaml"):
        load_env()
        self.config = load_config(config_path)
        ensure_directories()

        global logger
        logger = initialize_from_config(config_path)
        self.logger = logger

        self.data_fetcher = DataFetcher(self.config)
        self.indicator = TechnicalIndicator(self.config)
        self.strategy = RSIMACDStrategy(self.config)
        self.broker = PaperBroker(self.config)
        self.portfolio = PortfolioManager(self.config, self.broker)
        self.risk_manager = RiskManager(self.config)
        self.visualizer = Visualizer(self.config)

        self.tickers: List[str] = self.config.get("strategy", {}).get(
            "tickers",
            ["005930", "000660", "035420", "005380", "068270"]
        )
        self.trading_interval = self.config.get("trading", {}).get("interval_minutes", 60)

        self.logger.info(
            f"TradingBot 초기화 완료: 종목={len(self.tickers)}개, "
            f"간격={self.trading_interval}분, "
            f"초기자본={self.portfolio.initial_cash:,.0f}원"
        )

    def run_once(self) -> None:
        self.logger.info("=" * 60)
        self.logger.info("매매 사이클 시작")

        start_date = self.config.get("data", {}).get("start_date", "2023-01-01")
        end_date = get_kst_now().strftime("%Y-%m-%d")

        self.logger.info(f"데이터 수집 시작: {len(self.tickers)}개 종목")
        price_data = self.data_fetcher.fetch_multiple_tickers(
            self.tickers, start_date, end_date, use_cache=True
        )
        if not price_data:
            self.logger.warning("수집된 데이터가 없습니다. 사이클 종료.")
            return

        portfolio_state = self.portfolio.get_state()
        self.logger.info(
            f"포트폴리오 상태: 현금={portfolio_state['cash']:,.0f}원, "
            f"보유종목={len(portfolio_state['holdings'])}개, "
            f"총자산={portfolio_state['total_value']:,.0f}원"
        )

        self.logger.info("매매 신호 생성 중...")
        raw_signals = self.strategy.generate_signals(price_data, portfolio_state)
        self.logger.info(f"생성된 신호: {len(raw_signals)}개")

        current_prices = {}
        for ticker, df in price_data.items():
            if not df.empty:
                current_prices[ticker] = df.iloc[-1].get("Close", 0)

        filtered_signals = self.risk_manager.apply_risk_filters(
            raw_signals, self.portfolio, current_prices
        )
        self.logger.info(f"리스크 필터 통과: {len(filtered_signals)}개")

        for signal in filtered_signals:
            try:
                order = self.broker.place_order(
                    ticker=signal.ticker,
                    side=signal.action,
                    quantity=signal.quantity,
                    price=signal.price,
                    order_type=OrderType.MARKET,
                    current_market_price=signal.price,
                )
                success = self.portfolio.execute_order(order, current_prices)
                if success:
                    self.logger.info(f"주문 실행 성공: {order}")
                else:
                    self.logger.warning(f"주문 실행 실패: {order}")
            except Exception as e:
                self.logger.error(f"주문 처리 중 오류: {e}")

        self.logger.info("매매 사이클 종료")
        self.logger.info("=" * 60)

    def run_scheduled(self) -> None:
        interval_minutes = self.trading_interval
        schedule.every(interval_minutes).minutes.do(self._scheduled_task)
        self.logger.info(f"스케줄러 시작: {interval_minutes}분 간격")
        while True:
            schedule.run_pending()
            time_module.sleep(10)

    def _scheduled_task(self) -> None:
        if not is_market_open():
            self.logger.debug("장 시간이 아닙니다. 스킵.")
            return
        try:
            self.run_once()
        except Exception as e:
            self.logger.error(f"스케줄 작업 중 오류: {e}", exc_info=True)

    def generate_report(self) -> None:
        start_date = self.config.get("data", {}).get("start_date", "2023-01-01")
        end_date = get_kst_now().strftime("%Y-%m-%d")
        price_data = self.data_fetcher.fetch_multiple_tickers(
            self.tickers, start_date, end_date, use_cache=True
        )
        self.visualizer.generate_report(self.portfolio, price_data)


def main():
    import argparse

    parser = argparse.ArgumentParser(description="KRX 가상 트레이딩 봇")
    parser.add_argument(
        "--mode",
        choices=["once", "scheduled", "report"],
        default="once",
        help="실행 모드: once(1회), scheduled(스케줄), report(리포트 생성)",
    )
    parser.add_argument(
        "--config",
        default="config/settings.yaml",
        help="설정 파일 경로 (기본: config/settings.yaml)",
    )
    args = parser.parse_args()

    bot = TradingBot(config_path=args.config)

    if args.mode == "once":
        bot.run_once()
        bot.generate_report()
    elif args.mode == "scheduled":
        bot.run_scheduled()
    elif args.mode == "report":
        bot.generate_report()
    else:
        logger.error(f"알 수 없는 모드: {args.mode}")


if __name__ == "__main__":
    main()
```

---

## 🔁 `backtest.py`

```python
"""
백테스팅 진입점
과거 데이터로 전략을 테스트하고 성과를 측정합니다.
"""

import sys
from pathlib import Path
from datetime import datetime, timedelta
from typing import Dict, List, Optional, Tuple

import pandas as pd
import numpy as np

sys.path.insert(0, str(Path(__file__).parent))

from src.utils.logger import logger, initialize_from_config
from src.utils.helpers import load_config, load_env, ensure_directories, get_kst_now
from src.data.fetcher import DataFetcher
from src.indicators.technical import TechnicalIndicator
from src.strategy.rsi_macd import RSIMACDStrategy
from src.broker.paper_broker import PaperBroker, OrderType
from src.portfolio.manager import PortfolioManager
from src.risk.manager import RiskManager
from src.reporter.visualizer import Visualizer


class Backtester:
    """
    백테스팅 엔진
    과거 데이터를 순차적으로 처리하며 매매 시뮬레이션을 수행합니다.
    """

    def __init__(self, config_path: str = "config/settings.yaml"):
        load_env()
        self.config = load_config(config_path)
        ensure_directories()
        self.logger = initialize_from_config(config_path)

        self.data_fetcher = DataFetcher(self.config)
        self.indicator = TechnicalIndicator(self.config)
        self.strategy = RSIMACDStrategy(self.config)
        self.broker = PaperBroker(self.config)
        self.portfolio = PortfolioManager(self.config, self.broker)
        self.risk_manager = RiskManager(self.config)
        self.visualizer = Visualizer(self.config)

        self.tickers = self.config.get("strategy", {}).get(
            "tickers",
            ["005930", "000660", "035420", "005380", "068270"]
        )
        self.portfolio_history: List[Dict] = []
        self.logger.info("Backtester 초기화 완료")

    def run(
        self,
        start_date: str,
        end_date: str,
        initial_cash: Optional[float] = None,
    ) -> pd.DataFrame:
        if initial_cash:
            self.portfolio.initial_cash = initial_cash
            self.portfolio.cash = initial_cash

        self.logger.info(
            f"백테스트 시작: {start_date} ~ {end_date}, "
            f"초기자본={self.portfolio.initial_cash:,.0f}원, "
            f"종목={self.tickers}"
        )

        price_data = self.data_fetcher.fetch_multiple_tickers(
            self.tickers, start_date, end_date, use_cache=True
        )

        if not price_data:
            self.logger.error("백테스트 데이터 없음")
            return pd.DataFrame()

        all_dates = sorted(
            set().union(*[df.index for df in price_data.values() if not df.empty])
        )
        self.logger.info(f"총 거래일 수: {len(all_dates)}")

        for i, date in enumerate(all_dates):
            daily_data = {}
            for ticker, df in price_data.items():
                if date in df.index:
                    daily_data[ticker] = df.loc[:date]

            portfolio_state = self.portfolio.get_state()
            raw_signals = self.strategy.generate_signals(daily_data, portfolio_state)

            current_prices = {}
            for ticker, df in daily_data.items():
                if not df.empty:
                    current_prices[ticker] = df.iloc[-1].get("Close", 0)

            filtered_signals = self.risk_manager.apply_risk_filters(
                raw_signals, self.portfolio, current_prices
            )

            for signal in filtered_signals:
                try:
                    order = self.broker.place_order(
                        ticker=signal.ticker,
                        side=signal.action,
                        quantity=signal.quantity,
                        price=signal.price,
                        order_type=OrderType.MARKET,
                        current_market_price=signal.price,
                    )
                    self.portfolio.execute_order(order, current_prices)
                except Exception as e:
                    self.logger.error(f"백테스트 주문 오류 ({date}): {e}")

            state = self.portfolio.get_state()
            total_value = state["cash"]
            for ticker, info in state["holdings"].items():
                current_price = current_prices.get(ticker, info["avg_price"])
                total_value += info["quantity"] * current_price

            record = {
                "date": date,
                "cash": state["cash"],
                "invested": state["total_invested"],
                "total_value": total_value,
                "holdings_count": len(state["holdings"]),
            }
            self.portfolio_history.append(record)

            if (i + 1) % 100 == 0:
                self.logger.info(f"백테스트 진행: {i+1}/{len(all_dates)}일")

        history_df = pd.DataFrame(self.portfolio_history)
        if not history_df.empty:
            history_df.set_index("date", inplace=True)

        self.logger.info("백테스트 완료")
        self._print_summary(history_df)
        self._save_results(history_df)

        return history_df

    def _print_summary(self, history: pd.DataFrame) -> None:
        if history.empty:
            return

        initial = self.portfolio.initial_cash
        final = history["total_value"].iloc[-1]
        total_return = (final - initial) / initial * 100

        cumulative = history["total_value"]
        running_max = cumulative.cummax()
        drawdown = (cumulative - running_max) / running_max * 100
        max_dd = drawdown.min()

        trades = self.broker.get_order_history()
        win_count = sum(1 for t in self.portfolio.trade_history if t["pnl"] > 0)
        total_closed = len(self.portfolio.trade_history)
        win_rate = win_count / total_closed * 100 if total_closed > 0 else 0

        self.logger.info("=" * 60)
        self.logger.info("백테스트 결과 요약")
        self.logger.info("-" * 60)
        self.logger.info(f"초기 자본: {initial:,.0f}원")
        self.logger.info(f"최종 자본: {final:,.0f}원")
        self.logger.info(f"총 수익률: {total_return:.2f}%")
        self.logger.info(f"최대 낙폭(MDD): {max_dd:.2f}%")
        self.logger.info(f"총 거래 횟수: {len(trades)}")
        self.logger.info(f"완료된 매도: {total_closed}")
        self.logger.info(f"승률: {win_rate:.2f}%")
        self.logger.info(f"최종 보유 종목: {len(self.portfolio.holdings)}개")
        self.logger.info("=" * 60)

    def _save_results(self, history: pd.DataFrame) -> None:
        if history.empty:
            return

        output_dir = Path(self.config.get("visualization", {}).get("output_dir", "results"))
        output_dir.mkdir(parents=True, exist_ok=True)

        csv_path = output_dir / "backtest_history.csv"
        history.to_csv(csv_path, encoding="utf-8-sig")
        self.logger.info(f"히스토리 저장: {csv_path}")

        trades = self.broker.get_order_history()
        trade_data = []
        for t in trades:
            trade_data.append({
                "order_id": t.order_id,
                "timestamp": t.timestamp,
                "ticker": t.ticker,
                "side": t.side,
                "quantity": t.filled_quantity,
                "price": t.filled_price,
                "commission": t.commission,
                "tax": t.tax,
                "status": t.status.value,
            })
        trade_df = pd.DataFrame(trade_data)
        trade_csv = output_dir / "backtest_trades.csv"
        trade_df.to_csv(trade_csv, index=False, encoding="utf-8-sig")
        self.logger.info(f"거래 내역 저장: {trade_csv}")

        self.visualizer.plot_portfolio_performance(history, save=True, show=False)
        self.visualizer.plot_equity_curve(history, save=True, show=False)

        if self.tickers:
            ticker = self.tickers[0]
            start_date = history.index[0].strftime("%Y-%m-%d")
            end_date = history.index[-1].strftime("%Y-%m-%d")
            price_data = self.data_fetcher.fetch_daily_price(ticker, start_date, end_date)
            if not price_data.empty:
                ticker_orders = [t for t in trades if t.ticker == ticker]
                self.visualizer.plot_trade_signals(price_data, ticker_orders, ticker, save=True)

        self.logger.info("백테스트 결과 저장 완료")


def main():
    import argparse

    parser = argparse.ArgumentParser(description="KRX 백테스팅")
    parser.add_argument("--start", default="2023-01-01", help="시작일 (YYYY-MM-DD)")
    parser.add_argument("--end", default=None, help="종료일 (YYYY-MM-DD, 기본 오늘)")
    parser.add_argument("--cash", type=float, default=None, help="초기 자본")
    parser.add_argument("--config", default="config/settings.yaml", help="설정 파일")
    args = parser.parse_args()

    if args.end is None:
        args.end = get_kst_now().strftime("%Y-%m-%d")

    backtester = Backtester(config_path=args.config)
    history = backtester.run(args.start, args.end, args.cash)

    if not history.empty:
        backtester._print_summary(history)


if __name__ == "__main__":
    main()
```

---

## 🧪 `tests/test_fetcher.py`

```python
"""
DataFetcher 테스트
"""

import pytest
import pandas as pd
from pathlib import Path
import sys

sys.path.insert(0, str(Path(__file__).parent.parent))

from src.data.fetcher import DataFetcher


class TestDataFetcher:
    """DataFetcher의 주요 기능을 테스트합니다."""

    @pytest.fixture
    def config(self):
        """테스트용 설정 딕셔너리"""
        return {
            "data": {
                "source": "pykrx",
                "start_date": "2024-01-01",
                "end_date": "2024-01-10",
                "cache_dir": "test_cache",
                "cache_expire_days": 1,
            }
        }

    @pytest.fixture
    def fetcher(self, config):
        """DataFetcher 인스턴스"""
        return DataFetcher(config)

    def test_fetch_real_ticker(self, fetcher):
        """실제 종목 코드로 데이터를 가져올 수 있는지 테스트"""
        df = fetcher.fetch_daily_price("005930", "2024-01-02", "2024-01-05", use_cache=False)
        assert not df.empty, "삼성전자 데이터가 비어있음"
        assert all(col in df.columns for col in ["Open", "High", "Low", "Close", "Volume"])

    def test_fetch_invalid_ticker(self, fetcher):
        """잘못된 종목 코드에 대해 에러 처리"""
        with pytest.raises(ValueError):
            fetcher.fetch_daily_price("", "2024-01-01", "2024-01-10")

    def test_fetch_date_format(self, fetcher):
        """날짜 형식 검증"""
        with pytest.raises(Exception):
            fetcher.fetch_daily_price("005930", "01-01-2024", "2024-01-10")

    def test_cache_and_fetch(self, fetcher, tmpdir):
        """캐시 저장 및 조회 테스트"""
        fetcher.cache.cache_dir = tmpdir
        df1 = fetcher.fetch_daily_price("005930", "2024-01-02", "2024-01-03", use_cache=False)
        df2 = fetcher.fetch_daily_price("005930", "2024-01-02", "2024-01-03", use_cache=True)
        assert not df1.empty
        assert not df2.empty
        cache_files = list(tmpdir.glob("*.csv"))
        assert len(cache_files) > 0

    def test_fetch_multiple(self, fetcher):
        """여러 종목 동시 수집 테스트"""
        tickers = ["005930", "000660"]
        result = fetcher.fetch_multiple_tickers(tickers, "2024-01-02", "2024-01-05", use_cache=False)
        assert len(result) == len(tickers)
        for ticker in tickers:
            assert ticker in result
            assert not result[ticker].empty
```

---

## 🖥️ 실행 방법

### 1. 환경 설정

```bash
# 프로젝트 디렉토리로 이동
cd trading_bot

# 가상환경 생성 및 패키지 설치
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt

# 필요한 디렉토리 생성
mkdir -p logs data_cache results
```

### 2. 설정 확인

- `config/settings.yaml`에서 전략 파라미터, 종목 코드, 초기 자본 등을 수정
- 종목 코드는 6자리 숫자 (예: `005930` = 삼성전자)

### 3. 단일 실행 (1회 매매 + 리포트 생성)

```bash
python main.py --mode once
```

### 4. 스케줄러 실행 (일정 간격 반복)

```bash
python main.py --mode scheduled
```

장 시간(09:00~15:30) 동안 설정된 간격(기본 60분)으로 반복 실행. `CTRL+C`로 종료.

### 5. 백테스팅 실행

```bash
python backtest.py --start 2023-01-01 --end 2024-12-31 --cash 10000000
```

### 6. 단위 테스트

```bash
# 전체 테스트
pytest tests/ -v

# 특정 파일
pytest tests/test_fetcher.py -v

# 커버리지 측정
pytest tests/ --cov=src --cov-report=html
```

### 7. 로그 확인

```bash
tail -f logs/trading.log
```

---

## ⚠️ 주의사항

- `pykrx`와 `FinanceDataReader`는 한국 주식 데이터를 실시간으로 제공하지만, 일부 제한이 있을 수 있습니다.
- 캐시는 `data_cache/` 디렉토리에 저장되며, 만료 기간(기본 7일) 이후 자동 삭제됩니다.
- **실제 투자에 사용하지 마세요** — 이 봇은 학습 및 시뮬레이션 목적으로만 제작되었습니다.

---

## 📋 전체 코드 요약

|모듈|역할|
|---|---|
|`config/settings.yaml`|모든 수치를 관리하여 매직 넘버 제거|
|`src/data/fetcher.py`|pykrx / FinanceDataReader 기반 데이터 수집 + 캐싱|
|`src/indicators/technical.py`|pandas-ta 기반 RSI, MACD, 볼린저 밴드 계산|
|`src/strategy/rsi_macd.py`|RSI + MACD 복합 신호 생성|
|`src/broker/paper_broker.py`|수수료·세금·슬리피지 반영 주문 시뮬레이션|
|`src/portfolio/manager.py`|평균 단가 계산, 손익 추적, 거래 내역 저장|
|`src/risk/manager.py`|손절/익절, 일일 손실 한도, 포트폴리오 위험도 체크|
|`src/reporter/visualizer.py`|matplotlib 정적 그래프 + plotly 인터랙티브 차트|
|`backtest.py`|과거 데이터 기반 전략 성능 백테스팅|

> 모든 함수는 한국어 docstring, 타입 힌트, 구체적인 예외 처리를 포함합니다.