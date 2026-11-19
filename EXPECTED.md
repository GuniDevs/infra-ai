# Expected results

`scripts/run_quality.sh clean` must report zero for everything and 100% coverage.
`scripts/run_quality.sh seeded` should show the counts below (measured with GCC 13,
clang-tidy 18, cppcheck, gcovr, lizard; exact counts can differ by tool version).

| Seeded file | Defect | Caught by |
|---|---|---|
| seeded/memory_bugs.cpp | heap overflow, use after free, leak | ASan (runtime), clang-tidy (clang-analyzer) |
| seeded/overflow.cpp | signed integer overflow | UBSan (runtime) |
| seeded/null_deref.cpp | possible null dereference | cppcheck, clang-tidy |
| seeded/complex.cpp | complexity 21 (limit 15) | lizard |
| seeded/uncovered.cpp | function no test calls | gcovr (total coverage drops to about 60%) |
| seeded/badformat.cpp | bad formatting | clang-format |
| seeded/duplicate_a.cpp / _b.cpp | copy-pasted block | lizard -Eduplicate |
| seeded/warnings.cpp | unused var/param, sign compare, fallthrough | compiler -Wall -Wextra |

Seeded run summary seen when this repo was built:
compiler_warnings 4, files_needing_format 1, cppcheck_findings 6, clang_tidy_findings 7,
sanitizer_failures 4 of 4 runtime defects detected, line_coverage_pct 60.0,
too_complex_functions 1, duplicate_blocks 1.
