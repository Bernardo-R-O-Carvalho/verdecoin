# 🌿 VerdeCoin (VDC)

> **Mercado de carbono popular — para quem age pelo clima, não para quem pode pagar por ele.**

VerdeCoin é uma criptomoeda fork do Bitcoin Core (v27.0) desenvolvida para o app **VerdeCoin**, plataforma que remunera pessoas físicas por ações climáticas reais, com foco em justiça ambiental e comunidades periféricas.

---

## O problema que resolve

O mercado de carbono tradicional foi desenhado para governos e grandes corporações. Países ricos compram créditos para continuar poluindo — e quem mais sofre com a crise climática (moradores de periferias, ilhas de calor, áreas de risco) não recebe nenhum incentivo financeiro para agir.

A VerdeCoin muda isso.

---

## Como funciona

```
Usuário age pelo clima
        ↓
Ação validada (Gov.br + geolocalização + confirmação coletiva)
        ↓
Usuário recebe VDC proporcional à ação e à zona territorial
(áreas mais vulneráveis recebem mais)
        ↓
Empresa compra VDC do usuário em dinheiro real
        ↓
Empresa recebe Selo de Contribuição Climática (PID)
        ↓
VDC retorna à PID e é redistribuído para novos usuários
```

**Exemplos de ações climáticas reconhecidas:**
- Compra de carro elétrico
- Plantio de árvores
- Instalação de painel solar
- Descarte correto de resíduos

---

## Proteção contra fraude

| Mecanismo | Como funciona |
|---|---|
| Gov.br | Elimina falsificação de identidade |
| Geolocalização obrigatória | Impede declarações remotas |
| Liberação gradual de créditos | Desincentiva abandono após recebimento |
| Auditoria aleatória | Verificação surpresa de ações declaradas |
| Banimento permanente | Tolerância zero para fraude confirmada |

---

## Especificações técnicas

| Parâmetro | Valor |
|---|---|
| Ticker | VDC |
| Supply total | 50.000.000 VDC |
| Modelo de emissão | Bloco 1 emite todos os 50M para a PID |
| Mineração pública | Não existe |
| Tempo por bloco | 2 minutos |
| Halving | A cada 262.800 blocos (~1 ano) |
| Prefixo de endereços | Começa com `V` |
| bech32 mainnet | `vdc` |
| bech32 testnet/signet | `tvdc` |
| bech32 regtest | `vcrt` |
| Porta mainnet | 42777 |
| Porta testnet | 42778 |

---

## Proof of Authority (PoA)

A VerdeCoin **não tem mineração pública**. Em vez de Proof of Work, adota um modelo de **Proof of Authority** onde apenas a PID (entidade gestora) valida e emite blocos.

Isso garante:
- **Sem desperdício energético** — nenhum minerador competindo por recompensa
- **Controle da emissão** — os 50M de VDC são pré-alocados à PID no bloco 1
- **Velocidade e previsibilidade** — blocos a cada 2 minutos sem variação
- **Alinhamento com o propósito climático** — uma criptomoeda de carbono não pode ter footprint de carbono alto

A recompensa de todos os blocos após o bloco 1 é **zero**. Novos VDC só entram em circulação quando a PID os distribui para usuários verificados.

---

## Rodando localmente (regtest)

### Requisitos
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

### Subir o nó e criar carteira da PID

```bash
# Subir o nó em regtest
rm -rf /tmp/vdc-test && mkdir /tmp/vdc-test
./src/bitcoind -regtest -datadir=/tmp/vdc-test -daemon
sleep 12

# Criar carteira e importar chave da PID
./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test createwallet "PID"
./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test -rpcwallet=PID importdescriptors \
  '[{"desc":"pk(cNM5biwD2vCCPmYCNcs8569WU9jnBXiX5DeP9QTeAXQGd7v2Zi5F)#f2sw8a7m","timestamp":0}]'

# Minerar bloco 1 (50M VDC para a PID) + 100 blocos para maturar
ADDR=$(./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test -rpcwallet=PID getnewaddress)
./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test generatetoaddress 101 $ADDR

# Verificar saldo
./src/bitcoin-cli -regtest -datadir=/tmp/vdc-test -rpcwallet=PID getbalance
# Resultado: 50000000.00000000
```

---

## Status do projeto

Este repositório foi desenvolvido durante o **VerdeCoin Hackathon**. O código atual é funcional em regtest e demonstra o fluxo completo de emissão e transferência de VDC.

**Implementado:**
- ✅ Fork do Bitcoin Core v27.0 com parâmetros VerdeCoin
- ✅ Genesis block com chave da PID
- ✅ Bloco 1 emite 50M VDC para a PID
- ✅ Proof of Authority (recompensa zero em todos os blocos exceto o 1)
- ✅ Prefixos de endereço, portas e identificadores de rede customizados

**Próximos passos:**
- [ ] Seeds DNS (`seed.verdecoin.org`)
- [ ] Servidor mainnet 24/7
- [ ] Multiplicador territorial por zona PID
- [ ] Integração com validação Gov.br e geolocalização
- [ ] SDK para integração com o app VerdeCoin

---

## Licença

Fork do [Bitcoin Core](https://github.com/bitcoin/bitcoin) — MIT License.  
Modificações VerdeCoin © 2025 PID — todos os direitos reservados.
