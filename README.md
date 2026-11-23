# Digital Currencies and Blockchain (CSET352)

Lab work for **CSET352: Digital Currencies and Blockchain**, B.Tech CSE, 5th semester (odd semester, 2026).

| | |
|---|---|
| **Name** | Archit Gupta |
| **Roll number** | S24CSEU1049 |
| **Batch** | 12 |

## Labs

Each lab's report is in its own folder under [`labs/`](labs).

| Lab | Topic | What it covers | Report |
|---|---|---|---|
| 1 | Bitcoin wallet setup | Install a wallet, open Testnet, and send and receive test BTC. | [Lab 1 report](labs/Lab1/BTech5thSem_CSET352_S24CSEU1049_Lab1.docx) |
| 2 | Testnet and Regtest | Receive and send on Testnet; mine 101 blocks on Regtest and move coins. | [Lab 2 report](labs/Lab2/BTech5thSem_CSET352_S24CSEU1049_Lab2.docx) |
| 3 | Bitcoin address generation | Walk through the private key → public key → hash → compressed address pipeline. | [Lab 3 report](labs/Lab3/BTech5thSem_CSET352_S24CSEU1049_Lab3.docx) |
| 4 | ECDSA signatures | Sign a message and see how different nonces (k) produce different (r, s) pairs. | [Lab 4 report](labs/Lab4/BTech5thSem_CSET352_S24CSEU1049_Lab4.docx) |
| 5 | HD wallets and BIP-39 | Seeds, mnemonic validation, child key derivation, a WIF encoder/decoder, and an xpub key-recovery attack. | [Lab 5 report](labs/Lab5/BTech5thSem_CSET352_S24CSEU1049_Lab5.docx) |
| 6 | ERC-20 marketplace dApp | A goods/services marketplace paid in an ERC-20 token, with on-chain seller ratings. | [Lab 6 report](labs/Lab6/BTech5thSem_CSET352_S24CSEU1049_Lab6.docx) |
| 7 | Raw transactions | Fund from a faucet, build a raw transaction, broadcast it by hand, and verify it on an explorer. | [Lab 7 report](labs/Lab7/BTech5thSem_CSET352_S24CSEU1049_Lab7.docx) |
| 8 | Mini Bitcoin explorer and web wallet | A web app with a block explorer page, mempool monitor, and Testnet wallet. | [Lab 8 report](labs/Lab8/BTech5thSem_CSET352_S24CSEU1049_Lab8.docx) |
| 9 | Bitcoin Script | Compare legacy and SegWit scripts, use btcdeb and an online debugger, and write a small stack interpreter for P2PKH/P2WPKH. | [Lab 9 report](labs/Lab9/BTech5thSem_CSET352_S24CSEU1049_Lab9.docx) |
| 10 | Auto loan marketplace on Rootstock | A Solidity contract and React dApp linking dealers, lenders, and customers. The code is in this repo's root. | [Lab 10 report](labs/Lab10/BTech5thSem_CSET352_S24CSEU1049_Lab10.docx) |

## Lab 10: Auto loan marketplace

A Solidity smart contract and a React + Vite dApp for vehicle loans. Dealers list cars, lenders post loan offers, and customers apply with a down payment held in escrow by the contract.

### Deployment

- **Network:** Rootstock Testnet (chain ID 31)
- **Contract:** `0x14940624C4696e93792fBE63fa42Da2aDdcAD16a` ([view on explorer](https://explorer.testnet.rootstock.io/address/0x14940624C4696e93792fBE63fa42Da2aDdcAD16a))
- **RPC:** `https://public-node.testnet.rsk.co`
- **ABI:** `artifacts/AutoLendingMarketplace.abi.json`

### How a loan works

1. The dealer lists a vehicle with `listVehicle` (details plus a hash of the VIN).
2. A lender posts an offer with `createOffer` (principal, APR, term).
3. The customer calls `applyForLoan` and sends the down payment, which the contract holds.
4. The dealer accepts or rejects the application. A rejection refunds the down payment.
5. The lender funds the exact principal with `fundLoan`.
6. The dealer calls `confirmDelivery`. The contract pays the dealer and starts a 30-day payment schedule.
7. The customer pays each installment with `repay`. If a payment is more than 7 days late, anyone can call `declareDefault`.

### Run it locally

You need Node.js 20 or newer and MetaMask.

```bash
npm install
npm run dev
```

Open the address Vite prints, connect MetaMask, and switch to Rootstock Testnet. Use test RBTC only; you can get some from the [Rootstock faucet](https://faucet.rootstock.io/).

### Redeploy the contract

1. Open `contracts/AutoLendingMarketplace.sol` in [Remix](https://remix.ethereum.org/) and compile with Solidity 0.8.20.
2. In Deploy & Run, choose **Injected Provider - MetaMask** on Rootstock Testnet and deploy.
3. Put the new address in `CONTRACT_ADDRESS` in `src/lib/contract.js`, and replace the ABI file with the one from Remix.

### Project layout

```
contracts/   Solidity source
src/         React interface (customer, dealer, and lender views)
src/lib/     Wallet connection and contract config
artifacts/   ABI and bytecode of the deployed contract
screenshots/ Interface preview (sample data, not on-chain records)
labs/        Lab reports 1 to 10
```

### Limitations

This is a teaching prototype. It has not been audited, so don't use it with real funds. Interest is simple interest over 30-day periods. Marking a loan as defaulted doesn't repossess anything in the real world. KYC, insurance, credit checks, and title documents stay off-chain; never put personal data on a public chain.
