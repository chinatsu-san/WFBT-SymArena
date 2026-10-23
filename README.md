# WFBT-SymArena

A low-latency trading-systems toolkit in C++20 for x86-64 Linux. It covers the
whole path from a market-data packet to an order decision:

* a price-time-priority matching engine,
* a Nasdaq TotalView-ITCH 5.0 decoder and book builder,
* a wire-to-book pipeline,
* shared-memory IPC,
* a pre-trade risk gateway with a hash-chained audit journal,
* a queue-aware L3 backtester,
* AVX2 options pricing, and
* a square-root market-impact model.

Every hot-path component has a slow, obviously-correct reference implementation
next to it. The tests check the fast code against that reference bit for bit.

Notes:

* The pipeline figure is software-only and in-process. It is not
network-card-to-wire tick-to-trade.
* The per-order latency figures include the timer.
* The IPC round trip runs between two distinct physical cores.

### Limitations

* **Full-day book rebuild.** Rebuilding the book for all symbols over a full
day runs at about 13.8 M msg/s of compute on this laptop. The order table
(128 MB) is much larger than the L3 cache, so the rebuild is bound by DRAM
latency. Several published single-core rebuilds are faster.
* **IPC tail.** Under WSL2, the IPC p99.99 round trip is about 6.8 us. That
tail comes from the host, not the ring, and is no better than other queues
measured on the same machine.
* **Hardware and data.** All figures are from one laptop and one trading day
(Nasdaq 2019-01-30). The test suite uses synthetic data only.
Nasdaq publishes sample ITCH files at https://emi.nasdaq.com/ITCH/.
