# 🌿 VerdeCoin (VDC)

> **A popular carbon market — for those who act on climate, not those who can afford to pollute.**

VerdeCoin is a cryptocurrency forked from Bitcoin Core (v27.0), built to power the **VerdeCoin** app — a platform that financially rewards individuals for real climate actions, with a focus on environmental justice and underserved communities.

---

## The Problem

Traditional carbon markets were designed for governments and corporations. Wealthy nations buy carbon credits to keep polluting — while those who suffer most from the climate crisis (residents of urban heat islands, flood-prone areas, and low-income neighborhoods) receive no financial incentive to act.

VerdeCoin changes that.

---

## How It Works

```
User performs a climate action
        ↓
Action is validated
        ↓
User receives VDC proportional to the action and territorial zone
(more vulnerable areas receive higher rewards)
        ↓
Company buys VDC from the user in real currency
        ↓
Company send VDC to PID and, in exchange, receives a Climate Contribution Seal issued by PID
        ↓
VDC returns to PID and is redistributed to new verified users
```

**Examples of recognized climate actions:**
- Purchasing an electric vehicle
- Planting trees
- Installing solar panels
- Proper waste disposal and recycling

---

## Technical Specifications

| Parameter | Value |
|---|---|
| Ticker | VDC |
| Total supply | 50,000,000 VDC |
| Emission model | Block 1 mints all 50M VDC to PID |
| Public mining | None |
| Block time | 2 minutes |
| Halving | Every 262,800 blocks (~1 year) |
| Address prefix | Starts with `V` |
| bech32 mainnet | `vdc` |
| bech32 testnet/signet | `tvdc` |
| bech32 regtest | `vcrt` |
| Mainnet port | 42777 |
| Testnet port | 42778 |

---

## Proof of Authority (PoA)

VerdeCoin has **no public mining**. Instead of Proof of Work, it uses a **Proof of Authority** model where only PID (the governing entity) validates and issues blocks.

This ensures:
- **No energy waste** — no miners competing for block rewards
- **Controlled supply** — all 50M VDC are pre-allocated to PID at block 1
- **Speed and predictability** — consistent 2-minute block times
- **Aligned with climate goals** — a carbon currency shouldn't have a carbon footprint

Block rewards after block 1 are **zero**. New VDC only enters circulation when PID distributes it to verified users.

---

## Running Locally (regtest)

### Requirements
- Ubuntu / WSL2
- Build tools: `autoconf`, `automake`, `libtool`, `pkg-config`, `libssl-dev`, `libboost-all-dev`

### Build

```bash
git clone https://github.com/Bernardo-R-O-Carvalho/verdecoin.git
cd verdecoin
git checkout verdecoin-main
./autogen.sh
./configure --without-miniupnpc
make -j4
```

### Start the node and set up the PID wallet

```bash
# Start regtest node
rm -rf /tmp/vdc-test && mkdir /tmp/vdc-test
./src/bitcoind -regtest -datadir=/tmp/vdc-test -daemon
sleep 12

# Create PID wallet and import key
./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test createwallet "PID"
./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test -rpcwallet=PID importdescriptors \
  '[{"desc":"pk(cNM5biwD2vCCPmYCNcs8569WU9jnBXiX5DeP9QTeAXQGd7v2Zi5F)#f2sw8a7m","timestamp":0}]'

# Mine block 1 (50M VDC to PID) + 100 blocks to mature the coinbase
ADDR=$(./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test -rpcwallet=PID getnewaddress)
./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test generatetoaddress 101 $ADDR

# Check balance
./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test -rpcwallet=PID getbalance
# Expected: 50000000.00000000
```

---

## Project Status

This repository was developed during the **VerdeCoin Hackathon**. The current codebase is fully functional in regtest and demonstrates the complete VDC issuance and transfer flow.

**Implemented:**
- ✅ Bitcoin Core v27.0 fork with VerdeCoin parameters
- ✅ Genesis block with PID key
- ✅ Block 1 mints 50M VDC to PID
- ✅ Proof of Authority (zero block reward after block 1)
- ✅ Custom address prefixes, ports, and network identifiers

**Roadmap:**
- [ ] DNS seeds (`seed.verdecoin.org`)
- [ ] 24/7 mainnet server
- [ ] Territorial multiplier by PID zone
- [ ] Gov.br identity and geolocation validation
- [ ] SDK for VerdeCoin app integration

---

## License

Forked from [Bitcoin Core](https://github.com/bitcoin/bitcoin) — MIT License.  
VerdeCoin modifications © 2026 PID — all rights reserved.
