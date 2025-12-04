## Student Information

**Name:** Desmond Farley-Williams
**Student ID:** 008508702
**Repository:** https://github.com/3750449/Project-5-2.git

---

## Collaboration & Sources

- **cppreference.com** — consulted for `std::fstream`, `std::tolower`, `std::filesystem`
- **geeksforgeeks.org** — cross-checked C++ I/O examples and syntax

---

##Implementation Details

This project replays LRU traces against a provided open-addressed hash table with two probe styles:

single (linear probing)

double (double hashing)

For each trace (e.g., lru_profile_N_4096_S_23.trace) the harness:

runs 1 warm-up (untimed) and 7 timed passes, reports median elapsed (ms)

resets the table between runs

---

##Files of interest

HashMapAnalysis_StudentFiles/HashTableDictionary.hpp|.cpp

Stats + maps: printStats(), printMask(), csvStatsHeader(), csvStats(), map/bitstring helpers

HashMapAnalysis_StudentFiles/main.cpp — trace replay harness (reads trace, times runs, prints CSV + maps)

HashMapAnalysis_StudentFiles/Operations.hpp — op struct (I key, E key)

HashMapAnalysis_StudentFiles/traceFiles/lru_profile_N_*_S_23.trace — provided LRU traces

timing/ — CSVs and captured maps (your outputs)

---

## Outputs

C:\Users\37504\Desktop\HashMapAnalysis_StudentFiles\HashMapAnalysis_StudentFiles\cmake-build-debug\HashTablesOpenAddressing.exe lru_profile_N_4096_S_23.trace
Clearing hash table...
Starting a run with N = 4096 and 109001 operations.
in run trace printing csv.
table_size,active,available,tombstones,total_probes,inserts,deletes,lookups,full_scans,compactions,max_in_table,availabl
e_pct,load_factor_pct,eff_load_factor_pct,tombstones_pct,average_probes,probe_type,compaction_state
5101,4096,0,1005,209266412,51657,47561,0,40311,0,4096,0,80,100,19,2109.157733,double,compaction_off
in run trace printing csv ends.
Elements in use map.
←[31m-←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m←[32m ←[0m←[32m ←[0m←[31m-←[0m
←[31m-←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m←[32m ←[0m←[31m-←[0m←[31m-←[0m←[32m ←[0m←[31m-←[0m←[31m-←[0m←[31m-←[0m

(…)

←[31m ←[0m←[32m ←[0m←[31m ←[0m←[31m ←[0m←[31m ←[0m←[31m ←[0m←[32m ←[0m←[32m ←[0m←[32m ←[0m←[31m ←[0m←[31m ←[0m←[32m ←[0m
←[32m ←[0m←[31m ←[0m←[31m ←[0m←[31m ←[0m.
←[31m ←[0m
    5101 table size:
    1005 cells marked as deleted.
    4096 active cells.
       0 available elements.
    4096 maximum number of values in the table ever.
205737623 total probes.

   51657 inserts.
   47561 deletes.
       0 lookups.
   39289 full scans.
       0 compactions.

       0% ratio of available elements.
      80% load factor.
     100% effective load factor.
      19% tombstone fraction.

2073.59 average number of probes (double probing, compaction off).
compaction_off double_probing 5101
1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111

(…)

1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
1

---

##How to build

g++

g++ -std=c++20 -O2 -Wall -Wextra -pedantic *.cpp -o lru_harness

CMake

mkdir -p build && cd build
cmake ..
make
# binary: ./lru_harness

# From project root
.\build\Release\HashTablesOpenAddressing.exe HashMapAnalysis_StudentFiles\traceFiles\lru_profile_N_4096_S_23.trace  > .\timing\lru\N_4096_double_compaction_off.txt

////

5.1 Expectations vs. Observations

Expectation (theory reminder):

Small N → single tends to be competitive/faster (1 hash + contiguous probes)
Large N → double tends to win (less clustering to fewer probes)

Observation (elapsed time vs N):

•  At N = 1024 (single, compaction_on): average_probes ≈ 14.938, eff_load_factor ≈ 93%, available ≈ 6%
•  At N = 1024 (double, compaction_on): average_probes ≈ 6.281,  eff_load_factor ≈ 93%, available ≈ 6%
•  At N = 1024, double has fewer probes than single

////

5.1.2 “Work” per op vs. wall-clock

•  average_probes = touched slots at N=1024:

    single = 14.938
    double = 6.281

•  Fewer probes because lower time

Filled line:

•  At N = 1024, the average_probes favors double, probes for double vs single is consistent with double’s advantage at higher occupancy even with compaction_on

////

5.1.3 Hashing cost & locality

•  single: one hash, contiguous walk
•  double: extra hash, non-contiguous jumps

Constant hashing costs dominate when probe paths are short as small N and at N=1024, paths are not tiny, but double’s lower clustering wins on probe count

////

5.1.4 Compaction effects

Compaction removes tombstones to shorter probe chains, but compaction can spike time

•  At N=1024 (single, compaction_on): 27 compactions
•  At N=1024 (double, compaction_on): 33 compactions
•  End-of-run occupancy (both): available ≈ 6%, tombstones ≈ 13–14%, eff_load_factor ≈ 93%

Filled line:

•  At N=1024, single triggered 27 compactions and double 33
•  After the last compaction, the average_probes from single was 14.938 and was double ≈ 6.281

////

5.1.5 Throughput & latency

•  Throughput = ops_total / elapsed_ms
•  Latency = elapsed_ms / ops_total

At N=1024 double’s much lower average_probes suggests higher throughput and lower latency than single

////

5.1.7 Maps & histograms
Using the combined ACTIVE+DELETED = 1 vs AVAILABLE = 0 bitstrings:

•  With compaction_on, AVAILABLE = 6% and DELETED = 13–14%
•  Multiple compactions occurred (single: 27; double: 33), so the tombstones are being reset

•  load_factor_pct = ACTIVE / M = 1024/1279 = 80%
•  tombstones_pct = DELETED / M = 176–177 / 1279 = 13–14%
•  eff_load_factor_pct = ACTIVE / (M − DELETED) = 1024 / (1279 − 176) ≈ 93%

