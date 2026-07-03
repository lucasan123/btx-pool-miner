# BTX GPU Miner — btx-pool.com

Open GPU miner for **BTX** (post-quantum MatMul Proof-of-Work). Mines on the public
pool **btx-pool.com** (PPLNS, paid by validated shares) or any BTX pool.

- **NVIDIA** GPUs: Ampere / Ada / Hopper / Blackwell (RTX 30/40/50, A/H/L, RTX PRO).
  Only the NVIDIA driver is required — the binary is self-contained (CUDA statically linked).
- Auto-restart on disconnect. Multi-GPU with `--gpu 0,1,2`.

## Downloads (this release)

| File | Platform |
|------|----------|
| `btx-miner-linux.tar.gz`   | Linux x86-64 (NVIDIA) |
| `btx-miner-windows.zip`    | Windows x64 (NVIDIA)  |
| `btx-address-generator.tar.gz` / `.zip` | Offline BTX address generator (Python) |

## Quick start

**Linux**
```
tar xzf btx-miner-linux.tar.gz && cd btx-miner-linux
# put your btx1... address in address.txt (or edit mina.sh), then:
./mina.sh            # optional: ./mina.sh myrigname
```

**Windows**
```
unzip btx-miner-windows.zip
# put your btx1... address in address.txt (or edit mina.bat), then double-click mina.bat
```

Pool: `btx-pool.com:3334` · Worker = your BTX address (`btx1...`), optional `.rigname`.

## Notes
- Need a BTX address? Use the address generator (offline, no dependencies) or any BTX wallet.
- The miner reports partial shares (PPLNS) and submits a full block when it wins; payout is
  split across miners by proven share work.
- Antivirus/SmartScreen may warn on GPU miners (common false-positive for mining software) —
  the source and binaries are provided here for transparency.

## Verify your download (optional)
Compare the SHA-256 in `SHA256SUMS.txt` with your downloaded files.
