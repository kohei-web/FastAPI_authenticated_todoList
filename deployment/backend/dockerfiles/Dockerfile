# syntax=docker/dockerfile:1

FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    POETRY_VERSION=1.8.3 \
    # Poetryでコンテナ内にvenvを作らずシステムにインストール
    POETRY_VIRTUALENVS_CREATE=false \
    # タイムゾーン（ログ整合のため）
    TZ=Asia/Tokyo

# ---- OS deps ----
# dev環境/UTで必要になりやすいもの（bcrypt, cffi, psycopg系使わなくても最低限）
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libffi-dev \
    libpq-dev \
    curl \
    vim \
    && rm -rf /var/lib/apt/lists/*

# ---- Workdir ----
WORKDIR /app

# ---- Poetry ----
RUN pip install --no-cache-dir "poetry==${POETRY_VERSION}"

# 依存解決のキャッシュを効かせるため、先に定義ファイルだけコピー
COPY deployment/backend/poetry/pyproject.toml ./pyproject.toml
COPY deployment/backend/poetry/poetry.lock* ./poetry.lock

# 本番（ランタイム最小）にするなら --no-dev を検討
# 今回はUT/Postman検証が目的なので dev 依存も入れておきます
RUN poetry install --no-interaction --no-ansi

# ---- project_dir ----
# COPY . .

# デフォルトの起動コマンドは docker-compose 側で指定（uvicorn）
# ここではポートだけ開けておく
EXPOSE 8000
