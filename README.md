# wallet-tracking-telegram-bot
This Solana Wallet Tracking Telegram bot for tracking wallet activities, transactions, and interactions with the Pump.fun platform.

## Contact Info
LinkedIn: [@dias-ishbulatov](https://www.linkedin.com/in/dias-ishbulatov)

Telegram: [@bitman09](https://t.me/@bitman09)


## Key Features
- Tracking Solana wallet transactions that interact with the Pump.fun
- Monitor wallet activities and provide real-time updates
- Designated Telegram channel for seamless tracking

## Clone the repo
```bash
git clone https://github.com/bitman310/wallet-tracking-telegram-bot
```

## Project Structure
- src/index.ts

```
import { WalletTracker } from "./utils";

async function main() {
  const tracker = new WalletTracker();
  await tracker.start();
}

main().catch(console.error);
```

- src/utils/utils.ts

```
import { LAMPORTS_PER_SOL } from "@solana/web3.js";

export async function getTransactionDetails(
  connection: any,
  signature: string
) {
  const txn = await connection.getParsedTransaction(signature, {
    maxSupportedTransactionVersion: 0,
    commitment: "confirmed",
  });

  if (txn?.meta && txn.transaction) {
    const instructions = txn.transaction.message.instructions;

    const timestamp = txn.blockTime
      ? new Date(txn.blockTime * 1000).toISOString()
      : new Date().toISOString();

    const preBalances = txn.meta.preBalances;
    const postBalances = txn.meta.postBalances;
    const balanceChange = (postBalances[0] - preBalances[0]) / LAMPORTS_PER_SOL;

    const details = {
      signature,
      timestamp,
      balanceChange: `${balanceChange} SOL`,
      sender: txn.transaction.message.accountKeys[0].pubkey.toString(),
      instructions: instructions.map((ix: any) => {
        if ("parsed" in ix) {
          return {
            program: ix.program,
            type: ix.parsed.type,
            receiver: ix.parsed.info.destination,
          };
        }
        return {
          programId: ix.programId.toString(),
        };
      }),
      logs: txn.meta.logs,
    };

    return details;
  }
}
```

- config.ts

```
import dotenv from "dotenv";
dotenv.config();

export const PUMP_FUN_ADDRESS = "Target PUMPFUN ADDRESS";

export const MAIN_WALLET_ADDRESS = process.env.MAIN_WALLET_ADDRESS || "";
export const TELEGRAM_BOT_TOKEN = process.env.TELEGRAM_BOT_TOKEN || "";
export const TELEGRAM_CHANNEL_ID = process.env.TELEGRAM_CHANNEL_ID || "";
export const SOLANA_RPC_URL = process.env.SOLANA_RPC_URL || "";

export const TRACKED_WALLETS_SIZE = 150;
```

