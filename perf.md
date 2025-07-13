# perf 

## retired vs executed
Retired is the total number of actual operations that would be executed on a naive cpu
Executed includes things such as speculatively excuted instructions

## syntax
```
perf stat -- a.exe
# this counts cpu clocks cycles (not reference/external cycles)
# use ref-cycles for that
perf stat -e cycles,instructions -- a.exe
# memory stats
perf stat -e mem_load_retired.l1_miss -- a.exe
# branch prediction
perf stat -e branches,brach-misses -- a.exe
```
