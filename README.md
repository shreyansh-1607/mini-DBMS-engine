# Mini-RDBMS

A relational database engine built from scratch in C++ — storage manager,
buffer pool, B+ tree indexing, a SQL parser/executor, transactions, and
crash recovery. See `docs/design.md` for scope and design decisions, made
before each phase's implementation.

Status: **Phase 0 — project skeleton.**

## Build

Requires CMake >= 3.16 and a C++17 compiler. GoogleTest is fetched
automatically by CMake (no manual vendoring).

```bash
mkdir build && cd build
cmake ..
cmake --build . -j
```

## Run

```bash
./mini_rdbms
```

## Test

```bash
ctest --output-on-failure
# or directly:
./rdbms_tests
```

## Roadmap

| Phase | Component | Status |
|---|---|---|
| 0 | Project setup | done |
| 1 | Disk manager | not started |
| 2 | Buffer pool manager | not started |
| 3 | Table heap / tuple storage | not started |
| 4 | Catalog | not started |
| 5 | B+ tree index | not started |
| 6 | SQL parser | not started |
| 7 | Query executor | not started |
| 8 | Transactions + concurrency control | not started |
| 9 | WAL + crash recovery | not started |
| 10 | Polish (CLI, benchmarks) | not started |
