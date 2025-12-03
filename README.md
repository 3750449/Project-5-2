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

This implements Batch-then-Drain traces → Priority Queue timing (Linear baseline, Binary heap, Binomial queue, Quadratic “Oracle”).

It replays batch_then_drain traces and records timing to CSV using the same pipeline as Huffman: generate traces → harness replay → CSV → plot.

Run protocol: 1 warm-up (untimed), then 7 timed passes, report median elapsed (ms).

Single-seed policy (to match the example): seed = 101.
Ns: 2^10 … 2^17.
CSV schema matches Huffman:
impl,profile,trace_path,N,seed,elapsed_ms,ops_total,inserts,findmins,deletemins,extractmins.

---

##Files of interest

HashTableDictionary.hpp / HashTableDictionary.cpp — printStats(), printMask(), csvStatsHeader(), csvStats(), printBeforeAndAfterCompactionMaps() or printActiveDeleteMap()

main.cpp — harness driver (reads a trace, replays operations, times runs, prints CSV + maps)

Operations.hpp — defines the I key / E key op structure

traces/ directory with lru_profile_N_*_S_23.trace

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

