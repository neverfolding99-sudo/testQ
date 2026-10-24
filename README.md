[README.md](https://github.com/user-attachments/files/33910929/README.md)
# MitID Admin — Clonet website (hej.rf.gd/panel-d8x3q)

Komplet statisk klon af: `https://hej.rf.gd/panel-d8x3q/?i=1`

## Sådan åbner du siden lokalt (med demo-backend)

```
python -m http.server 8000 --directory "C:\Users\johnn\Desktop\hej_clone\mirror"
```

Åbn derefter: **http://127.0.0.1:8000/**

App'en kalder normalt panelets backend (`api.secretvoip.eu` mv.), som
svarer 401 uden en rigtig admin-session. For at se hele panelet er der
inkluderet en **lokal mock-backend** (`assets/mock-api.js`) der fanger
alle udgående API-kald og svarer med demo-data. Mød mocken giver:

- Licens-tjekket grønt lys (ellers blokeres panelet med
  "Licens udløbet eller ugyldig")
- Log ind med **hvilket som helst** password (mocken accepterer alt)
- Demo-data i dashboard/statistik/visitor-logs
- Alle kald logges i konsollen (`window.__mockLog`) og via `[MOCK]`-tags

BEMÆRK: Mocken rører ALDRIG den rigtige backend — alt kører lokalt.

## Filer

```
hej_clone/
└── mirror/
    ├── index.html                      <- Hoved-side (React-skal)
    ├── panel-d8x3q/index.html          <- Original sti (virker også)
    └── assets/
        ├── admin-BOZZIQ_J.js           <- Hele panelet (obfuskeret) 437 KB
        ├── vendor-react-sicEs9XM.js    <- React vendor 316 KB
        ├── vendor-qr-4HvgGXca.js       <- QR-kode-bibliotek 414 KB
        ├── browser-ClpSNpCR.js         <- Licens-tjek + browser-modul 21 KB
        ├── browser-Sjzlfjd_.css        <- Styles 88 KB
        ├── favicon.png                 <- Ikon
        ├── mock-api.js                 <- LOKAL DEMO-BACKEND (tilføjet)
        ├── deobfuscated_strings.json   <- ALLE 3514 afkodede strenge
        ├── endpoints_and_urls.txt      <- Endpoints/URL'er
        └── features_and_labels.txt     <- Alle funktioner/etiketter
```

## Fuld API-overflade (fundet ved at køre panelet med mock)

**Licens (kræves før panelet starter):**
```
POST https://secretvoip.com/license/validate.php
     body: {"license_key":"cust-secrets","secret":"your-real-secret-here"}
```

**Login:**
```
POST https://api.secretvoip.eu/api/public/admin-verify
     body: {"password":"..."}
```

**Victim-side / public (polles konstant):**
```
GET /api/public/user-logins
GET /api/public/otp-entered
GET /api/public/password-entered
GET /api/public/sms-codes
```

**Settings (styres fra Admin Panel):**
```
GET /api/public/settings/show-sms
GET /api/public/settings/sms-amount
GET /api/public/settings/connection-text
GET /api/public/settings/broadcast-message
GET /api/public/settings/broadcast-type
GET /api/public/settings/show-swipe
GET /api/public/settings/show-otp
GET /api/public/settings/otp-code
GET /api/public/settings/show-password
```

**Admin:**
```
GET /api/admin/visitor-logs
```

## Hvad siden er

En single-page app (React) kaldet **"MitID Admin"** — et phishing/admin-panel
til MitID (dansk nationalt digitalt ID). Siden er beskyttet af en
JS-challenge (slowAES) på `?i=1` → `?i=2` → ... som er løst under kloning.

## Funktioner fundet i koden (afkodede strenge)

### Login / Autentifikation
- Log ind / Log på / Log ud / Logget ud
- Login-lås (Login-lås) og låsning
- Session (Session st, Session ud, n_session)
- Adgangskode (password, password_e, password_t, gangskode, dgangskode)
- OTP (otp_entered, otp_toggle, OTP Aktiv/Inaktivti)
- SMS-koder (SMS Koder, sms_toggle, SMS felt s, SMS koder.)
- Swipe-side (swipe_toggle, Swipe Aktiv/Inaktiv) — simulerer MitID-appens swipe
- Engangskode, gentag godkendelseskode, skjul/vis adgangskode

### QR-kode
- Aktiv QR-kode, QR-kode ryd, Test QR send, skjul QR-kode
- QR-kode-rydning

### Beløb (falsk bank-transaktion)
- Vælg beløb, Beløb ved, Beløb ryddet, Uden beløb, For kort

### Statusmeddelelser
- Blå MitID (info), Grøn/Succes, Rød/Advarsel
- ✅ Succes, ⚠️ Advarsel, ℹ️ MitID
- Fejl ved ændring, succes, advarsel

### Besøgslogs (Visitor logs)
- Besøgslogs, Logs, _logs
- Browser:, OS:, IP-adresse (ipAddress)
- Bruger-ID, Bruger sen, Send bruger
- Download (CSV: text/csv)

### Licenser
- Licenser, License validation, Licens ikke gyldig, Licenssystem

### Broadcast
- Broadcast (besked til aktive sider)

### Backend / endpoints (fragmenter)
- `/validate.`, `/licenses`, `/license`, `/test-`, `/panel`
- `https://secretvoip.com`, `api.secretvoip.eu`, `paycrivo.com`
- `staging--5ymyn3kg2eq20wt6xn8a.youbase.cloud` (CSP connect-src)
- `https://public.youware.com/...` (assets-CDN)
- `https://www.mitid.dk` (officiel MitID-side, brugt til redirect-tricket)
- `https://hej.rf.gd/` (anti-bot challenge `?i=1..N`)

### localStorage-nøgler (klient-side data)
- `mitid_user`, `mitid_channel`, `mitid_dark`, `mitid_show`, `mitid_sound`
- `mitid_admin`, `n_token`, `n_login_at`, `n_session`, `uniqueUser`, `userid`

## Sikkerheds-/analyse-noter
- Panelet er obfuskert med javascript-obfuscator (string-array, 4783 strenge).
  3514 unikke indekser blev afkodet ved at køre bundlen i en sandbox.
- Brug kun til autoriseret sikkerhedsanalyse.
