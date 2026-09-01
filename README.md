# web3-tools

[![tests](https://github.com/N23eos/web3-tools/actions/workflows/tests.yml/badge.svg)](https://github.com/N23eos/web3-tools/actions/workflows/tests.yml)
[![license: MIT](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

**web3-tools is a Python CLI that generates EVM wallets and searches for vanity addresses.** A vanity search matches a hex pattern at the start, at the end or anywhere in the address, case-insensitively, and uses every CPU core unless told otherwise. Generated wallets are written as CSV or JSON, with or without seed phrases, and one address works on every EVM chain. Before a search it prints a difficulty estimate, so the cost of a longer pattern is visible up front. The only dependency is eth-account, and the suite runs on Python 3.9, 3.12 and 3.14.

<div align="center">

[![Star on GitHub](https://img.shields.io/github/stars/N23eos/web3-tools?style=for-the-badge&logo=github&label=Star%20this%20repo&color=FFD700&labelColor=1a1a1a)](https://github.com/N23eos/web3-tools)

</div>

## Install

```bash
git clone https://github.com/N23eos/web3-tools && cd web3-tools
pip install .
```

Requires Python 3.9+.

## Usage

### Generate wallets

```bash
web3-tools generate -n 10 -o wallets.csv   # 10 wallets with seed phrases → CSV
web3-tools generate -n 5 -o wallets.json   # JSON output
web3-tools generate --no-mnemonic          # raw keypair, printed to terminal
```

### Find a vanity address

```bash
web3-tools vanity dead                       # address starting with 0xdead
web3-tools vanity cafe --position suffix     # ending with ...cafe
web3-tools vanity 7777 --position anywhere   # 7777 anywhere in the address
web3-tools vanity c0ffee --count 3 -o found.csv
```

Matching is case-insensitive. Search uses all CPU cores by default
(`--workers N` to limit). Add `--mnemonic` if you want seed phrases for the
found wallets (search runs slower).

### How long will it take?

Each extra pattern character multiplies the expected search time by 16:

| Pattern length | Expected attempts | Rough time (8 cores) |
|---|---|---|
| 3 | 4,096 | < 1 s |
| 4 | 65,536 | seconds |
| 5 | ~1M | ~1 min |
| 6 | ~17M | ~15 min |
| 7 | ~268M | hours |
| 8 | ~4.3B | days |

The CLI shows the estimate before starting and asks for confirmation on hard
patterns (`--yes` to skip).

## Security

- Output files contain **private keys and seed phrases in plaintext**. Anyone
  with the file controls the wallets. Don't commit them, don't sync them to
  cloud storage unencrypted.
- Keys are generated locally with [eth-account](https://github.com/ethereum/eth-account);
  nothing is sent anywhere.
- For wallets that will hold significant funds, prefer a hardware wallet.

## Development

```bash
pip install -e ".[dev]"
pytest
```
