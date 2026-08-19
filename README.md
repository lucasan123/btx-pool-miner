# BTX GPU Miner — btx-pool.com

# btx-rc-miner v4 — GPU miner for BTX (MatMul v4.7 Epoch-A) · pool btx-pool.com

Miner for the post-fork BTX chain (block 185000+). Mines on the public pool **btx-pool.com:3334**
(stratum v4, vardiff ≈ 1 share/min per connection, PPLNS on validated work, 3% fee).

## Downloads
| File | What | SHA-256 |
|---|---|---|
| `btx-rc-miner-linux-x86_64.tar.gz` | Linux x86-64 miner (`btx-rc-miner`, `mine.sh`, `README.txt`) | see `SHA256SUMS.txt` |
| `btx-rc-miner-windows-x64.zip` | Windows x64 miner (`btx-rc-miner.exe`, `MINE.bat`, `BENCH.bat`, `BENCH-ALL-GPUS.bat`, `README.txt`) | see `SHA256SUMS.txt` |
| `btx-address-generator.tar.gz` / `.zip` | Offline BTX address generator (Python) | see `SHA256SUMS.txt` |
| `SHA256SUMS.txt` | Checksums of all the files above | — |

## Requirements
- NVIDIA GPU: binaries contain **RTX 50xx (sm_120, tested by us)**, **RTX 40xx (sm_89)** and **RTX 30xx (sm_86)**. 40xx/30xx are compiled in but not benchmarked by us — the built-in self-test says at once whether the GPU is bit-exact.
- NVIDIA driver **580+**; **CUDA ≥ 13.2 / cuBLASLt ≥ 13.4** (`libcublasLt.so.13` / `cublasLt64_13.dll`) — we test with CUDA Toolkit 13.3. CUDA 13.0/13.1 give different results: the miner refuses to start.
- Linux: glibc ≥ 2.35 (Ubuntu 22.04+). Windows: VC++ Redistributable 2015-2022 x64.

## Quick start
Linux
```
tar xzf btx-rc-miner-linux-x86_64.tar.gz && cd btx-rc-miner-linux-x86_64
nano mine.sh          # put your btx1… address in ADDRESS (POOL is already btx-pool.com:3334)
./mine.sh
```
Windows: unzip, open `MINE.bat` with Notepad, set `ADDRESS`, save, double-click `MINE.bat`.

Command line (both): `btx-rc-miner --stratum=btx-pool.com:3334 --user=btx1youraddress.rigname --gpus=N` (first N GPUs) or `--devices=0,2` (exactly these GPUs, nvidia-smi ids). In `mine.sh`/`MINE.bat`: `GPUS=N` or `DEVICES=0,2`.

## Notes
- The miner first runs `--bench=1`: it must print `GPU bit-exact    : YES` and `[expected: OK]`. If not, it stops on purpose.
- `--gpus=N` / `--devices=LIST` start one process per GPU (one connection each); the dashboard shows the machine as one row "rig ×N". `mine.sh`/`MINE.bat` self-test every selected GPU first.
- Windows: extract the whole zip to a folder before running the .bat files; `BENCH-ALL-GPUS.bat` detects the GPU count with nvidia-smi.
- The miner's console output is in English.
- Every share is re-computed by the pool on GPU; rewards are split by proven work. Dashboard: http://btx-pool.com:8090
