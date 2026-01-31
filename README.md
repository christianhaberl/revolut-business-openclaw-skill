# Revolut Business API — OpenClaw Skill

Full CLI for **Revolut Business** — accounts, balances, transactions, counterparties, payments, FX exchange, CSV export.

> ⚠️ **Business only** — Revolut Personal API requires PSD2 Open Banking (AISP) registration and is not supported.

## Features

- 💰 **Accounts & Balances** — list all accounts, total EUR balance
- 📋 **Transactions** — filter by date, type, account; JSON output
- 👥 **Counterparties** — list, search by name
- 💸 **Payments** — send payments (with confirmation) or create drafts
- 💱 **FX Exchange** — exchange currencies between accounts
- 🔄 **Internal Transfers** — move funds between own accounts
- 📊 **CSV Export** — export transactions for bookkeeping
- 🔑 **Auto Token Refresh** — OAuth tokens refresh automatically

## Setup

### Prerequisites
- Python 3.10+
- `pip install PyJWT cryptography`
- Revolut Business account

### 1. Generate API Certificate
```bash
mkdir -p ~/.clawdbot/revolut
openssl genrsa -out ~/.clawdbot/revolut/private.pem 2048
openssl req -new -x509 -key ~/.clawdbot/revolut/private.pem -out ~/.clawdbot/revolut/certificate.pem -days 730 -subj "/CN=openclaw"
```

### 2. Upload to Revolut
1. Go to **business.revolut.com** → Settings → API
2. **Add Certificate**: paste content of `certificate.pem`
3. Set **OAuth redirect URI** (e.g. `https://yourdomain.com/callback`)
4. Set **IP whitelist** to your server's public IP
5. Copy the **Client ID**

### 3. Configure Environment
```bash
# In your .env
REVOLUT_CLIENT_ID=your_client_id_here
REVOLUT_ISS_DOMAIN=yourdomain.com   # redirect URI domain without https://
```

### 4. Authenticate
```bash
python3 scripts/revolut.py auth          # shows consent URL
python3 scripts/revolut.py auth --code <CODE>   # exchange code for tokens
```

## Usage

```bash
revolut accounts            # List all accounts with balances
revolut balance             # Total EUR balance
revolut tx -n 20            # Last 20 transactions
revolut tx --since 2026-01-01 --to 2026-01-31
revolut cp                  # List counterparties
revolut pay -c "Name" --amount 50.00 -r "Reference"
revolut fx --amount 100 --sell EUR --buy USD
revolut export -o transactions.csv
revolut token-info          # Show token status
```

## Security

- Private key and tokens stored in `~/.clawdbot/revolut/` — treat as sensitive
- Payments require explicit confirmation (use `--yes` to skip, `--draft` for approval in app)
- Never share your private key, tokens, or client assertion JWT

## License

MIT
