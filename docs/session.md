# Session Management Guide

## How Sessions Work

```
Pair phone number → session/creds.json is created → encode to Base64 → store as SESSION_ID
```

- **Local**: Bot uses `session/creds.json` directly
- **Heroku/Sevalla**: Bot decodes `SESSION_ID` env var → writes `session/creds.json` on startup

---

## Step 1 — Generate SESSION_ID from creds.json

Run this command from the project root after pairing:

```bash
node -e "const fs=require('fs'); const raw=fs.readFileSync('./session/creds.json','utf8'); const b64=Buffer.from(raw).toString('base64'); console.log('\n=== YOUR SESSION_ID ===\n'); console.log(b64); console.log('\n=== END SESSION_ID ===\n'); console.log('Length:', b64.length, 'chars');"
```

## Step 2 — Verify it is valid

```bash
node -e "require('dotenv').config(); const j=JSON.parse(Buffer.from(process.env.SESSION_ID,'base64').toString('utf8')); console.log('registered:', j.registered); console.log('me.id:', j.me.id); console.log('name:', j.me.name);"
```

Expected output:
```
registered: true
me.id: YOUR_NUMBER:XX@s.whatsapp.net
name: YOUR_NAME
```

## Step 3 — Update SESSION_ID

- **Local** → paste into `.env` as `SESSION_ID=<base64>`
- **Heroku** → Settings → Config Vars → `SESSION_ID`
- **Sevalla** → Environment Variables → `SESSION_ID`

---

## Every Time You Re-Pair

1. Bot saves new `session/creds.json`
2. Run the command in Step 1
3. Copy the full Base64 output
4. Update `SESSION_ID` in `.env` AND on your hosting platform
5. Redeploy