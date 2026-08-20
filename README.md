# btx-rc-miner — GPU miner for BTX (MatMul v4.7 "Epoch-A")

Standalone GPU miner for the post-fork BTX chain (block 185000+), built for the
public pool **[btx-pool.com](https://btx-pool.com)**. A mining attempt is no longer
a hash: it is a full MatMul *episode* (~141 trillion multiply-accumulates), computed
entirely on the GPU and verified bit-exactly by the pool.

- **No node needed** — the miner only talks to the pool you configure.
- **Open, auditable payouts** — coinbase is split on-chain among miners in
  proportion to verified work (30-min window), pool fee 1.5%.
- **Self-testing** — on every start each GPU must reproduce the reference
  digests bit-exactly, or the miner refuses to run.

## Downloads

Grab the latest packages from the **[Releases page](../../releases/tag/v4)**:

| File | Size | What it is |
|---|---|---|
| `btx-rc-miner-windows-x64.zip` | ~3 MB | the miner (Windows x64) |
| `btx-rc-miner-linux-x86_64.tar.gz` | ~3 MB | the miner (Linux x86-64) |
| `btx-cuda-runtime-windows-x64.zip` | ~385 MB | *optional, one-time*: CUDA libraries, if you don't have the CUDA Toolkit installed |
| `btx-cuda-runtime-linux-x86_64.tar.gz` | ~400 MB | same, for Linux |
| `SHA256SUMS.txt` | — | checksums of all of the above |

## Requirements

1. An **NVIDIA GPU**: RTX 50xx (tested), RTX 40xx (sm_89) and RTX 30xx (sm_86)
   kernels are all included. The built-in self-test tells you immediately
   whether your card computes bit-exactly; `BENCH.bat` / `--bench` tells you how fast it is.
2. **NVIDIA driver 580 or newer** (any 580+ "Game Ready" or "Studio" driver
   from nvidia.com). The start scripts check this for you.
3. **CUDA libraries** (cublasLt 13.4+), one of:
   - download the `btx-cuda-runtime-*` package from the release and extract it
     **into the miner folder** (one time, no installation), **or**
   - have the CUDA Toolkit **13.3** installed (13.0/13.1 are NOT enough — they
     compute different results and the miner stops by itself).

## Quick start

**Windows**
1. Extract `btx-rc-miner-windows-x64.zip` to a folder.
2. No CUDA Toolkit? Extract `btx-cuda-runtime-windows-x64.zip` into the *same* folder.
3. Open `MINE.bat` with Notepad and put your BTX address in `ADDRESS`.
   All your GPUs are used by default; set `GPUS=2` or `DEVICES=0,2` to restrict.
4. Save and double-click `MINE.bat`.

**Linux**
```bash
tar xzf btx-rc-miner-linux-x86_64.tar.gz && cd <folder>
# no CUDA Toolkit? one-time:
tar xzf ../btx-cuda-runtime-linux-x86_64.tar.gz
nano mine.sh      # put your BTX address in ADDRESS (all GPUs used by default)
./mine.sh
```

Your rigs, hashrate, share of the pool and found blocks show up live on
**[btx-pool.com](https://btx-pool.com)**. No account: the BTX address *is* the account.

## Reliability (v1.1)

The miner **reconnects by itself** if the pool connection drops (2→60 s backoff),
detects dead/hung connections within 120 s, and the multi-GPU supervisor
**restarts a GPU process that dies** (driver reset etc.) after 10 s. The start
scripts add an outer restart loop as a last-resort safety net — you can leave a
rig unattended.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `cublasLt64_13.dll not found` | extract `btx-cuda-runtime-windows-x64.zip` into the miner folder, or install CUDA Toolkit 13.3 |
| closes instantly / "could not start" | NVIDIA driver older than 580 — update it |
| `GPU bit-exact : NO` | CUDA too old (13.0/13.1) — use the runtime package or Toolkit 13.3 |
| `connection ... failed` | check the pool address (`btx-pool.com:3334`) and firewall/antivirus |

More in the `README.txt` inside each package.

## Pool

- Stratum v4: `btx-pool.com:3334` — dashboard: [btx-pool.com](https://btx-pool.com)
- Fee 1.5% — payouts in every found block's coinbase, proportional to verified work.
- Three mesh-connected pool nodes relay every found block to each other and to
  the network within milliseconds.
