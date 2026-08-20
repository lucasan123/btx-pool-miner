# btx-rc-miner — GPU miner for BTX (MatMul v4.7 "Epoch-A")

Standalone GPU miner for the post-fork BTX chain (block 185000+), built for the
public pool **[btx-pool.com](https://btx-pool.com)**. A mining attempt is no longer
a hash: it is a full MatMul *episode* (~141 trillion multiply-accumulates), computed
entirely on the GPU and verified bit-exactly by the pool.

- **No node needed** — the miner only talks to the pool you configure.
- **Self-contained (v4.1)** — the tensor-core GEMM is compiled into the binary:
  **no CUDA Toolkit, no runtime download, no DLLs**. Only an NVIDIA driver 580+.
- **Open, auditable payouts** — coinbase is split on-chain among miners in
  proportion to verified work (30-min window), pool fee 1.5%.
- **Self-testing** — on every start each GPU must reproduce the reference
  digests bit-exactly, or the miner refuses to run.

## Downloads

Grab the latest packages from the **[Releases page](../../releases/latest)**:

| File | Size | What it is |
|---|---|---|
| `btx-rc-miner-standalone-windows-x64.zip` | ~3 MB | **the miner (Windows x64), self-contained — start here** |
| `btx-rc-miner-standalone-linux-x86_64.tar.gz` | ~4 MB | **the miner (Linux x86-64), self-contained — start here** |
| `btx-rc-miner-hiveos-1.2.tar.gz` | ~6 MB | HiveOS custom miner package (flight-sheet Installation URL), self-contained |
| `btx-modded-matador-linux-x86_64.tar.gz` | ~27 MB | alternative miner: modded build of the open-source matador (no dev-fee, fast block submit) |
| `btx-modded-matador-1.0.tar.gz` | ~27 MB | the same, as a HiveOS custom miner package |
| `btx-rc-miner-windows-x64.zip` / `-linux-x86_64.tar.gz` | ~3 MB | classic cuBLASLt build — needs CUDA 13.3+ installed (or the `btx-cuda-runtime-*` package from the [v4 release](../../releases/tag/v4)) |
| `btx-address-generator.*` | <1 MB | offline BTX address generator (single file) |
| `SHA256SUMS.txt` | — | checksums of all of the above |

## Requirements

1. An **NVIDIA GPU**: RTX 50xx (tested), RTX 40xx (sm_89) and RTX 30xx (sm_86)
   kernels are all included. The built-in self-test tells you immediately
   whether your card computes bit-exactly; `BENCH.bat` / `--bench` tells you how fast it is.
2. **NVIDIA driver 580 or newer** (any 580+ "Game Ready" or "Studio" driver
   from nvidia.com). The start scripts check this for you.
3. That's it for the standalone builds. (The classic builds additionally need
   the CUDA 13.3 Toolkit or the `btx-cuda-runtime-*` package.)

## Quick start

**Windows**
1. Extract `btx-rc-miner-standalone-windows-x64.zip` to a folder.
2. Open `MINE.bat` with Notepad and put your BTX address in `ADDRESS`.
   All your GPUs are used by default; set `GPUS=2` or `DEVICES=0,2` to restrict.
3. Save and double-click `MINE.bat`.

**Linux**
```bash
tar xzf btx-rc-miner-standalone-linux-x86_64.tar.gz && cd btx-rc-miner-standalone
nano mine.sh      # put your BTX address in ADDRESS (all GPUs used by default)
./mine.sh
```

**HiveOS**: Custom miner — Installation URL
`https://github.com/lucasan123/btx-pool-miner/releases/download/v4.1/btx-rc-miner-hiveos-1.2.tar.gz`,
Miner name `btx-rc-miner`, wallet `%WAL%`, Pool URL `btx-pool.com:3334`. Driver 580+
(`nvidia-driver-update 580`). No runtime download step anymore.

Your rigs, hashrate, share of the pool and found blocks show up live on
**[btx-pool.com](https://btx-pool.com)**. No account: the BTX address *is* the account.

## Multi-GPU rigs (v4.1)

One process per GPU is managed for you. Every log line is tagged with its GPU,
and the miner periodically prints the whole rig's total — the same ep/s number
the pool dashboard shows:

```
[GPU 0] 345 episodes, 1.08 s/episode = 0.928 ep/s, 2 shares accepted
[GPU 1] 320 episodes, 1.16 s/episode = 0.866 ep/s, 1 shares accepted
  RIG TOTAL: 3.627 ep/s on 4 GPUs, 1340 episodes, 5 shares accepted
```

## Reliability

The miner **reconnects by itself** if the pool connection drops (2→60 s backoff),
detects dead/hung connections within 120 s, and the multi-GPU supervisor
**restarts a GPU process that dies** (driver reset etc.) after 10 s. The start
scripts add an outer restart loop as a last-resort safety net — you can leave a
rig unattended.

First run on a new machine spends a few extra seconds auto-tuning the GEMM tile
configurations for your GPU model (every candidate is bit-identical — the tuner
only ever picks a speed, never a result); the winner is cached on disk.

## Troubleshooting

| Symptom | Fix |
|---|---|
| closes instantly / "could not start" | NVIDIA driver older than 580 — update it |
| `GPU bit-exact : NO` | driver too old or unsupported GPU — run `--bench=1` and read the reason line |
| `connection ... failed` | check the pool address (`btx-pool.com:3334`) and firewall/antivirus |
| `cublasLt64_13.dll not found` (classic build only) | use the standalone build instead, or install CUDA Toolkit 13.3 |

More in the README inside each package.

## Pool

- Stratum v4: `btx-pool.com:3334` — dashboard: [btx-pool.com](https://btx-pool.com)
- Fee 1.5% — payouts in every found block's coinbase, proportional to verified work.
- Three mesh-connected pool nodes relay every found block to each other and to
  the network within milliseconds.
