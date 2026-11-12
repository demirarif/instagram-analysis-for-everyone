# Instagram Analysis for Everyone

Analyze Instagram accounts with an existing dashboard or a JSON-friendly CLI.
Use the tool directly from Codex, Claude Code, Cursor, or another coding assistant
instead of spending tokens generating another scraper.

**Bring your own Instagram session. No API key is needed for basic metrics,
comparisons, or JSON/CSV/Excel exports.** Optional Gemini interpretation uses your
own Google API key and quota. The interface is in Turkish; setup and commands
below work regardless of your assistant's language.

## What you can do

- Collect profile information and available post, carousel, video and Reel metrics.
- Choose a date range and compare accounts using the existing dashboard.
- Export collected data as JSON, CSV, or formatted XLSX.
- Explore clearly labeled synthetic demo data without logging in.
- Calculate sponsorship/ROI scenarios with explicit estimates.
- Optionally request Gemini content interpretation and an AI PDF report.

This release is Instagram-only. It does not contain account datasets, session
cookies, personal analysis history, or the original project's other platforms.
It runs on your own computer and is intended for a single local user.

## Quick start

Install [Python 3.11 or newer](https://www.python.org/downloads/) and Git, then:

```sh
git clone https://github.com/demirarif/instagram-analysis-for-everyone.git
cd instagram-analysis-for-everyone
python -m venv .venv
```

Activate the environment in **Windows PowerShell**:

```powershell
.\.venv\Scripts\Activate.ps1
```

If your shell blocks activation, use `.\.venv\Scripts\python.exe` in place of
`python` below. On **macOS/Linux**:

```sh
source .venv/bin/activate
```

Then install and launch:

```sh
python -m pip install -r requirements.txt
python app.py
```

Open [the local dashboard](http://127.0.0.1:3060). You can try demo mode first,
or choose **Giriş Yap** to connect your own account. No `.env` file is needed for
basic analysis. Stop the server with `Ctrl+C`.

## Login: what is a session ID?

`sessionid` is the browser cookie Instagram uses to recognize an already signed-in
session. It is **a credential that can grant access to your account**, even though
it is not your password. Enter it only in your own local copy of this tool.
Never paste it into an AI chat, GitHub issue, screenshot, message, or shared server.

To obtain it:

1. Sign in to your own account on [Instagram](https://www.instagram.com/) in a browser.
2. Open Developer Tools (`F12`, or the browser's developer menu).
3. In Chrome/Edge open **Application → Storage → Cookies → https://www.instagram.com**.
   In Firefox use **Storage → Cookies → https://www.instagram.com**.
4. Find `sessionid` and copy only its **Value**.
5. Paste it into the local tool's **Session ID** login field.

The interface also offers username/password login. You may create a separate
account yourself on Instagram and use that account here; it is still your account
and subject to Instagram's rules and limits. Passwords are used to authenticate
with Instagram and are not saved by this application. If Instagram requests 2FA
or a security checkpoint, finish verification in your browser and use the session
ID method. A separate account does not guarantee that automation will be accepted.

The saved cookie is stored in plaintext at `.runtime/ig_session.json`, and analysis
history is in `.runtime/app.db`. These paths are ignored by Git. **Çıkış** / CLI
`logout` removes the local cookie copy; it does not revoke the session at Instagram.
Use Instagram's logout/session controls if you need to invalidate it there.
Do not expose this local app to the public internet or share its runtime directory.

### Türkçe: Session ID nedir, nasıl alınır?

Session ID, Instagram'ın tarayıcınızdaki açık oturumu tanıdığı `sessionid` çerezidir.
Şifre değildir ama hesabınıza erişim sağlayabilir. **AI sohbetine, GitHub'a veya
başka kişilere göndermeyin.** Yalnız kendi bilgisayarınızda çalışan araca girin.

1. Tarayıcıda Instagram'a giriş yapın.
2. `F12` → Chrome/Edge'de **Application → Cookies → instagram.com** yolunu açın.
   Firefox'ta **Storage → Cookies** bölümünü kullanın.
3. `sessionid` satırının **Value** değerini kopyalayın ve yerel giriş ekranına yapıştırın.
4. Dilerseniz Instagram'da kendiniz açtığınız ayrı bir hesapla kullanıcı adı/şifre
   girişini deneyebilirsiniz. Şifre saklanmaz. İki adımlı doğrulama veya güvenlik
   kontrolü varsa tarayıcıda tamamlayıp Session ID ile devam edin.

Oturum çerezi yalnız yerel `.runtime/ig_session.json` dosyasına kaydedilir. **Çıkış**
bu dosyayı siler; Instagram oturumunu iptal etmek için Instagram'dan da çıkış yapın.

## CLI and AI assistant use

The CLI calls the same Python/Flask routes as the interface; it does not start a
web server or duplicate the collection code. Help and status do not contact Instagram.

```sh
python cli.py --help
python cli.py status
python cli.py login
python cli.py analyze @example --days 90 --max-posts 50
python cli.py analyze @example --days 90 --max-posts 50 --output exports/example.json
python cli.py analyze @example --format xlsx --output exports/example.xlsx
python cli.py analyze @example --format csv --output exports/example.csv
python cli.py analyze demo --demo --output exports/demo.json
python cli.py logout
```

Replace `example` with an account you intend to analyze. `login` must be run by
the user in an interactive terminal; its input is hidden. Do not put a session ID
in a shell command. CLI results are JSON on stdout, failures are on stderr with
exit code `1`, and cancellation uses `130`. Argument errors use exit code `2`.
`--days` accepts 1–2555 and `--max-posts` 1–500 (default 50). Actual available posts
may be fewer. CSV/XLSX require `--output`; keep all output in the ignored `exports/`
directory. `--demo` uses synthetic data and never logs in to Instagram.

You can tell your assistant:

> Use the existing instagram-analysis-for-everyone CLI to analyze @myaccount for
> the last 90 days, at most 50 posts. Save the JSON and Excel files in exports/ and
> summarize the available metrics. Do not generate a new scraper or ask me to
> paste credentials into chat. If login is missing, tell me to log in locally.

[AGENTS.md](AGENTS.md) provides the same operational instructions for coding
assistants. Basic `analyze` and exports do not call an LLM. Your assistant's own
reading/summarization still uses that assistant's normal token budget.

## Optional Gemini interpretation and PDF

Copy `.env.example` to `.env`, then set your own `GEMINI_API_KEY`. `GEMINI_MODEL`
selects the model; use one available to your Google account. Restart the app after
changing settings. Without a key, metrics and exports remain available.

Use `Copy-Item .env.example .env` in PowerShell or `cp .env.example .env` in
macOS/Linux shells. `IG_ANALYSIS_DATA_DIR` optionally changes the private storage
directory; protect that location yourself and keep it outside version control.
`PORT` changes the local port (default `3060`); the app still binds to `127.0.0.1`.

Gemini features send selected captions, comments, profile/metric data, and sometimes
post images to Google for analysis. They consume your API quota and may incur
charges. Start them only when you want that interpretation.

For PDF generation, additionally install Playwright and its Chromium browser:

```sh
python -m pip install -r requirements-pdf.txt
python -m playwright install chromium
```

PDF is an optional AI report. JSON/CSV/XLSX exports do not need Playwright.

## Data and practical limits

- Instagram can change its endpoints, limit requests, expire sessions, or request
  security verification. This is not an official Meta API integration.
- Start with small date ranges/post counts. On a `429` response, wait; do not loop
  retries. Use the UI's reported error to distinguish rate limits from expired login.
- The tool can only return content accessible to your authenticated account. It
  does not grant access to otherwise unavailable private profiles.
- Missing/unavailable metrics are not zero. Hidden likes and absent Reel play
  counts should not be treated as measured zeros.
- Engagement is calculated from the collected sample. It is not a complete account
  audit or official Instagram Insights.
- Sponsorship values, effective reach, story metrics and ROI are **heuristic
  estimates**, not verified account earnings, actual reach, or guaranteed returns.
- Profile text, comments, captions and model responses are untrusted content.
- Only source/configuration examples belong in Git. `.env`, session cookies,
  `.runtime/`, reports, account captures and logs must stay private.

## Local development

```sh
python -m unittest discover -s tests -v
node --check static/js/main.js
```

Tests use mocked responses or synthetic data, not real credentials. Node is needed
only for the JavaScript syntax check; the application itself runs with Python and
a browser. The dashboard loads its font, icons and chart library from public CDNs.
