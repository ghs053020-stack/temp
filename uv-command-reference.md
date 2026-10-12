# uv 명령어 레퍼런스

[uv](https://docs.astral.sh/uv/)는 Rust로 작성된 빠른 Python 패키지/프로젝트 관리자입니다.
`pip`, `pip-tools`, `pipx`, `poetry`, `pyenv`, `virtualenv`를 하나로 대체할 수 있습니다.

---

## 1. 설치 및 업데이트

```powershell
# Windows (PowerShell)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# pip로 설치
pip install uv
```

| 명령어 | 설명 |
|---|---|
| `uv --version` | 설치된 uv 버전 확인 |
| `uv self update` | uv 자체를 최신 버전으로 업데이트 (독립 설치 시) |
| `uv help <command>` | 명령어 도움말 보기 |

---

## 2. 프로젝트 관리

### 프로젝트 생성

| 명령어 | 설명 |
|---|---|
| `uv init` | 현재 디렉터리에 프로젝트 초기화 (`pyproject.toml`, `.python-version` 등 생성) |
| `uv init my-project` | 새 디렉터리에 프로젝트 생성 |
| `uv init --app` | 애플리케이션 프로젝트로 생성 (기본값) |
| `uv init --lib` | 라이브러리(패키지) 프로젝트로 생성 (`src/` 레이아웃) |
| `uv init --package` | 빌드 가능한 패키지 형태의 앱 생성 |
| `uv init --python 3.12` | 특정 Python 버전으로 초기화 |

### 의존성 관리

| 명령어 | 설명 |
|---|---|
| `uv add requests` | 의존성 추가 (`pyproject.toml` + `uv.lock` 갱신 + 설치) |
| `uv add "fastapi>=0.110"` | 버전 제약과 함께 추가 |
| `uv add --dev pytest` | 개발용 의존성 추가 (`dev` 그룹) |
| `uv add --group lint ruff` | 특정 의존성 그룹에 추가 |
| `uv add --optional web flask` | optional-dependencies(extra)에 추가 |
| `uv add -r requirements.txt` | requirements 파일의 패키지 일괄 추가 |
| `uv add git+https://github.com/user/repo` | Git 저장소에서 추가 |
| `uv add --editable ./libs/mylib` | 로컬 경로를 editable로 추가 |
| `uv remove requests` | 의존성 제거 |
| `uv remove --dev pytest` | 개발 의존성 제거 |

### 잠금 파일 및 동기화

| 명령어 | 설명 |
|---|---|
| `uv lock` | `uv.lock` 생성/갱신 |
| `uv lock --upgrade` | 모든 패키지를 허용 범위 내 최신으로 갱신 |
| `uv lock --upgrade-package requests` | 특정 패키지만 업그레이드 |
| `uv lock --check` | lock 파일이 최신인지 확인 (CI용) |
| `uv sync` | lock 파일 기준으로 가상환경(`.venv`) 동기화 |
| `uv sync --frozen` | lock 파일 갱신 없이 그대로 설치 |
| `uv sync --locked` | lock 파일이 최신이 아니면 오류 |
| `uv sync --no-dev` | 개발 의존성 제외하고 설치 |
| `uv sync --all-extras` | 모든 extra 포함 설치 |
| `uv sync --extra web` | 특정 extra 포함 설치 |
| `uv sync --group lint` | 특정 그룹 포함 설치 |
| `uv sync --all-packages` | 워크스페이스 전체 멤버 설치 |

### 실행

| 명령어 | 설명 |
|---|---|
| `uv run python main.py` | 프로젝트 환경에서 스크립트 실행 (자동 sync) |
| `uv run pytest` | 프로젝트 환경의 명령 실행 |
| `uv run --with rich main.py` | 임시로 추가 패키지를 포함해 실행 |
| `uv run --python 3.11 main.py` | 특정 Python 버전으로 실행 |
| `uv run --no-sync main.py` | 동기화 없이 실행 |
| `uv run --env-file .env main.py` | `.env` 파일 로드 후 실행 |

### 조회 / 빌드 / 배포

| 명령어 | 설명 |
|---|---|
| `uv tree` | 의존성 트리 출력 |
| `uv tree --outdated` | 최신 버전이 있는 패키지 표시 |
| `uv tree --depth 1` | 트리 깊이 제한 |
| `uv export --format requirements-txt > requirements.txt` | lock 파일을 requirements.txt로 내보내기 |
| `uv version` | 프로젝트 버전 확인 |
| `uv version --bump minor` | 프로젝트 버전 올리기 (`major` / `minor` / `patch`) |
| `uv build` | sdist + wheel 빌드 (`dist/`) |
| `uv publish` | PyPI 등에 배포 |

---

## 3. 스크립트 (단일 파일)

PEP 723 인라인 메타데이터를 사용해 단일 스크립트의 의존성을 관리합니다.

| 명령어 | 설명 |
|---|---|
| `uv init --script example.py` | 인라인 메타데이터가 포함된 스크립트 생성 |
| `uv add --script example.py requests` | 스크립트에 의존성 추가 |
| `uv run example.py` | 격리된 환경에서 스크립트 실행 |
| `uv lock --script example.py` | 스크립트용 lock 파일 생성 |

```python
# /// script
# requires-python = ">=3.12"
# dependencies = ["requests"]
# ///
import requests
print(requests.get("https://example.com").status_code)
```

---

## 4. Python 버전 관리

| 명령어 | 설명 |
|---|---|
| `uv python list` | 설치 가능/설치된 Python 버전 목록 |
| `uv python install 3.12` | Python 설치 |
| `uv python install 3.11 3.12 3.13` | 여러 버전 동시 설치 |
| `uv python uninstall 3.11` | Python 제거 |
| `uv python find` | 현재 사용될 Python 경로 확인 |
| `uv python pin 3.12` | `.python-version` 파일에 버전 고정 |
| `uv python dir` | uv가 관리하는 Python 설치 경로 |
| `uv python upgrade` | 설치된 Python의 패치 버전 업그레이드 |

---

## 5. 도구(Tool) 관리 — `pipx` 대체

| 명령어 | 설명 |
|---|---|
| `uvx ruff check .` | 도구를 설치 없이 임시 환경에서 실행 (`uv tool run`의 별칭) |
| `uvx ruff@0.6.0 --version` | 특정 버전으로 실행 |
| `uvx --from httpie http` | 패키지명과 실행 명령이 다를 때 |
| `uv tool install ruff` | 도구를 전역으로 설치 (PATH에 등록) |
| `uv tool list` | 설치된 도구 목록 |
| `uv tool upgrade ruff` | 도구 업그레이드 |
| `uv tool upgrade --all` | 모든 도구 업그레이드 |
| `uv tool uninstall ruff` | 도구 제거 |
| `uv tool update-shell` | 도구 실행 경로를 PATH에 추가 |
| `uv tool dir` | 도구 설치 경로 확인 |

---

## 6. pip 호환 인터페이스

기존 `pip` / `pip-tools` / `virtualenv` 워크플로를 그대로 빠르게 사용할 때 씁니다.

### 가상환경

| 명령어 | 설명 |
|---|---|
| `uv venv` | `.venv` 가상환경 생성 |
| `uv venv --python 3.12` | 특정 Python 버전으로 생성 |
| `uv venv myenv` | 이름 지정해 생성 |
| `.venv\Scripts\activate` | 활성화 (Windows) |
| `source .venv/bin/activate` | 활성화 (macOS/Linux) |

### 패키지 설치/제거

| 명령어 | 설명 |
|---|---|
| `uv pip install requests` | 패키지 설치 |
| `uv pip install -r requirements.txt` | requirements 파일로 설치 |
| `uv pip install -e .` | 현재 프로젝트를 editable 설치 |
| `uv pip install --system requests` | 시스템 Python에 설치 |
| `uv pip uninstall requests` | 패키지 제거 |
| `uv pip list` | 설치된 패키지 목록 |
| `uv pip show requests` | 패키지 정보 |
| `uv pip freeze` | 설치된 패키지를 requirements 형식으로 출력 |
| `uv pip tree` | 의존성 트리 |
| `uv pip check` | 의존성 충돌 확인 |

### 컴파일/동기화 (`pip-tools` 대체)

| 명령어 | 설명 |
|---|---|
| `uv pip compile requirements.in -o requirements.txt` | 고정된 requirements 생성 |
| `uv pip compile pyproject.toml -o requirements.txt` | pyproject 기반으로 생성 |
| `uv pip compile --upgrade requirements.in` | 최신 버전으로 재컴파일 |
| `uv pip sync requirements.txt` | 환경을 파일과 정확히 일치시킴 (불필요 패키지 제거) |

---

## 7. 캐시 관리

| 명령어 | 설명 |
|---|---|
| `uv cache dir` | 캐시 디렉터리 경로 |
| `uv cache clean` | 전체 캐시 삭제 |
| `uv cache clean requests` | 특정 패키지 캐시 삭제 |
| `uv cache prune` | 사용하지 않는 캐시 정리 |

---

## 8. 자주 쓰는 전역 옵션

| 옵션 | 설명 |
|---|---|
| `-p, --python <ver>` | 사용할 Python 버전/경로 지정 |
| `-q, --quiet` / `-v, --verbose` | 출력 줄이기 / 자세히 |
| `--offline` | 네트워크 없이 캐시만 사용 |
| `--no-cache` | 캐시 사용 안 함 |
| `--index-url <url>` | 기본 패키지 인덱스 변경 |
| `--extra-index-url <url>` | 추가 패키지 인덱스 |
| `--directory <path>` | 지정 디렉터리에서 명령 실행 |
| `--project <path>` | 지정 프로젝트 기준으로 실행 |

---

## 9. 주요 환경 변수

| 변수 | 설명 |
|---|---|
| `UV_PYTHON` | 기본 Python 버전 |
| `UV_INDEX_URL` / `UV_DEFAULT_INDEX` | 기본 패키지 인덱스 |
| `UV_CACHE_DIR` | 캐시 경로 |
| `UV_PROJECT_ENVIRONMENT` | 프로젝트 가상환경 경로 (기본 `.venv`) |
| `UV_LINK_MODE` | 설치 방식 (`copy`, `hardlink`, `symlink`) |
| `UV_NO_CACHE` | 캐시 비활성화 |

---

## 10. 빠른 워크플로 예시

```bash
# 새 프로젝트 시작
uv init my-app && cd my-app
uv add fastapi uvicorn
uv add --dev pytest ruff
uv run uvicorn main:app --reload

# 기존 프로젝트 클론 후 환경 구성
git clone <repo> && cd <repo>
uv sync

# 테스트 및 린트
uv run pytest
uvx ruff check .

# 의존성 업그레이드
uv lock --upgrade
uv sync
```

### pip/poetry → uv 대응표

| 기존 | uv |
|---|---|
| `python -m venv .venv` | `uv venv` |
| `pip install x` | `uv add x` (프로젝트) / `uv pip install x` |
| `pip install -r requirements.txt` | `uv pip install -r requirements.txt` |
| `pip freeze` | `uv pip freeze` |
| `pipx run x` / `pipx install x` | `uvx x` / `uv tool install x` |
| `pyenv install 3.12` | `uv python install 3.12` |
| `poetry install` | `uv sync` |
| `poetry add x` | `uv add x` |
| `poetry run x` | `uv run x` |
| `poetry lock` | `uv lock` |
| `poetry build` / `poetry publish` | `uv build` / `uv publish` |
