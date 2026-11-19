# cpp-quality-testbed

Small CMake C++17 project for testing a code-quality pipeline.
- `src/`, `tests/test_core.cpp`: clean code that must pass every check
- `seeded/`, `tests/test_seeded.cpp`: intentional defects, one per quality tool (see EXPECTED.md)

## Run
    sudo apt install cmake cppcheck clang-tidy clang-format
    pip install lizard gcovr
    ./scripts/run_quality.sh clean     # expect all zeros
    ./scripts/run_quality.sh seeded    # expect findings from every tool

Reports land in `reports/` (logs per tool and summary.json). Thresholds: CCN_LIMIT (default 15),
COV_MIN (default 80, informational in this script; enforce it in your gate).

## Use with your pipeline
Point your orchestrator at `scripts/run_quality.sh seeded` and map each log to your shared
findings format. A correct pipeline must PASS on `clean` and FAIL on `seeded`.

## Push to GitHub
    git init && git add . && git commit -m "C++ quality testbed"
    gh repo create cpp-quality-testbed --public --source=. --push
