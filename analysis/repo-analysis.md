# Repository Analysis: PP-AR-T/catalyst

## 1. Repo purpose
Catalyst is a deprecated Python-based algorithmic trading framework for crypto assets. It combines strategy authoring, historical backtesting, performance analytics, and live-exchange execution behind a Zipline-like API and a `catalyst` CLI.

## 2. Main folders and components
- `/catalyst`: core library.
  - `algorithm.py`, `api.py`: strategy lifecycle and user-facing API.
  - `exchange/`: live/backtest exchange integration and exchange-specific execution flow.
  - `finance/`: orders, slippage, commission, performance/risk tracking.
  - `data/`: bundles, ingestion, pricing readers/writers.
  - `pipeline/`: factor/filter pipeline engine.
  - `utils/`: calendars, CLI helpers, validation, caching, run orchestration.
  - `examples/`: sample strategies.
- `/tests`: extensive nose-based test suite across algorithm, data, exchange, finance, risk, pipeline.
- `/docs`: Sphinx documentation and tutorials.
- `/etc`: requirements and environment definitions.
- `/conda`: conda recipes for dependencies.

## 3. Key files to read first
1. `/home/runner/work/catalyst/catalyst/README.rst` (repo-relative: `README.rst`; status, scope, high-level intent).
2. `/home/runner/work/catalyst/catalyst/setup.py` (repo-relative: `setup.py`; entry points, compiled extensions, dependency model).
3. `/home/runner/work/catalyst/catalyst/catalyst/__main__.py` (repo-relative: `catalyst/__main__.py`; CLI surface and operational flows).
4. `/home/runner/work/catalyst/catalyst/catalyst/algorithm.py` (repo-relative: `catalyst/algorithm.py`; core strategy runtime contract).
5. `/home/runner/work/catalyst/catalyst/catalyst/exchange/exchange_algorithm.py` (repo-relative: `catalyst/exchange/exchange_algorithm.py`; crypto exchange-specific behavior).
6. `/home/runner/work/catalyst/catalyst/.travis.yml` (repo-relative: `.travis.yml`; historical lint/test commands).
7. `/home/runner/work/catalyst/catalyst/docs/source/install.rst` (repo-relative: `docs/source/install.rst`; expected runtime/install model).

## 4. Main language/frameworks/dependencies
- Language: Python (classifiers include 2.7/3.4/3.5 in `setup.py`; CI matrix uses 2.7/3.6 in `.travis.yml`), plus Cython extensions (`*.pyx`).
- Core frameworks/libraries: pandas, numpy, scipy, statsmodels, SQLAlchemy, click, logbook.
- Trading/exchange: ccxt, custom exchange adapter layer.
- Packaging/build: setuptools + Cython/native extension build steps.
- Testing/linting: nose/nosetests, flake8.
- Docs: Sphinx.

## 5. How the project appears to run or test
- CLI entrypoint from `setup.py`: `catalyst = catalyst.__main__:main`.
- Typical use: run backtests/live workflows via `catalyst` CLI commands and strategy scripts.
- Historical CI (`.travis.yml`):
  - `flake8 catalyst tests`
  - `cd tests && nosetests`
- Docs build path via `/docs/Makefile` (`make html` invokes extension build then Sphinx).
- In this environment, historical tools (`flake8`, `nosetests`) were not installed, indicating modern setup drift from original CI assumptions.

## 6. Useful concepts worth preserving
- Clear strategy lifecycle model (`initialize`, `handle_data`, `before_trading_start`, `analyze`).
- Separation of concerns among market data, simulation clock, order handling, and performance tracking.
- Exchange abstraction layer that isolates broker/exchange specifics.
- Pipeline-style factor/filter computation pattern.
- Strong test coverage pattern around simulation behavior and edge cases.

## 7. Code that may be worth reusing
- Conceptual API and lifecycle contracts in `/catalyst/algorithm.py` and `/catalyst/api.py`.
- Exchange domain abstractions in `/catalyst/exchange/` (interface shapes, error taxonomy, execution flow ideas).
- Performance/risk accounting structure in `/catalyst/finance/performance/` and `/catalyst/finance/risk/`.
- Calendar/time/event orchestration ideas in `/catalyst/utils/calendars/`, `/catalyst/utils/events.py`, `/catalyst/gens/`.
- Test scenario design patterns in `/tests/test_tradesimulation.py` and related simulation tests.

## 8. Code that should not be migrated
- Legacy Python 2/early Python 3 compatibility scaffolding.
- Cython-heavy internals as-is (port design, not direct implementation).
- Deprecated ecosystem pinning and old dependency versions from `/etc/requirements*.txt`.
- Legacy CI/release machinery tied to Travis/AppVeyor-era assumptions.
- Tight coupling to now-stale external services/endpoints (including old marketplace/exchange assumptions).

## 9. Risks and hidden assumptions
- Repo is explicitly deprecated (maintenance ended in 2018).
- Dependency pins are old and may contain security/compatibility debt.
- Architectural assumptions are bound to pandas/numpy-era in-memory pipelines and Python object models.
- Exchange integrations likely assume historical CCXT/API behaviors that have changed.
- Build/test workflow depends on toolchain and binary dependencies (Cython/TA-Lib/conda layouts) that may be fragile today.

## 10. Recommended classification
**Concept rewrite**

Rationale: this codebase is valuable for architecture and domain behavior, but is too old and coupled to legacy Python/Cython/dependency constraints for direct migration.

## 11. Suggested target mapping into a future Rust repo
- **domain**: asset/trading pair/order/portfolio types from `algorithm`, `assets`, `finance` models.
- **market-data**: bundle ingestion/readers from `data/` and exchange market data adapters.
- **strategy**: lifecycle contract and scheduling semantics from `algorithm.py` + `utils/events.py`.
- **execution-sim**: order matching/fill/slippage/commission simulation concepts from `finance/` + `gens/`.
- **risk**: exposure/performance/risk metrics from `finance/risk` and `finance/performance`.
- **backtest**: simulation engine orchestration from `gens/tradesimulation` and CLI run flow.
- **storage**: bundle persistence and historical data storage abstractions from `data/`.
- **agent-support**: CLI orchestration, config loading, and extension hooks from `__main__.py` and `utils/run_algo.py`.
- **docs**: preserve conceptual docs/tutorial intent from `docs/source/` and examples.

## 12. One smallest safe next step
Create a Rust design note that formalizes Catalyst’s strategy lifecycle and event clock contract (inputs, outputs, and state transitions) using only behavior-level semantics from `algorithm.py` and `gens/tradesimulation`, without porting code.
