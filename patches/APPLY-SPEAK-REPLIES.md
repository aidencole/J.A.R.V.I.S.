# Apply: Local Kokoro speak-replies (free)

This patch adds spoken chat replies using **local Kokoro TTS** (no paid APIs).

## What it adds

- Server endpoint: `POST /v1/speech/synthesize`
- TTS health: `GET /v1/speech/tts/health`
- Settings toggle: **Speak replies**
- Auto-plays assistant answers when that toggle is on

## Apply on your Windows OpenJarvis fork

In **PowerShell**:

```powershell
cd C:\Users\aiden\Documents\AI-Projects\OpenJarvis
git checkout development
git status
```

Download and apply the patch (from this J.A.R.V.I.S. helper repo once pushed):

```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/aidencole/J.A.R.V.I.S./cursor/local-kokoro-speak-replies-6f09/patches/0001-local-kokoro-speak-replies.patch" -OutFile "$env:TEMP\speak-replies.patch" -UseBasicParsing
git apply "$env:TEMP\speak-replies.patch"
```

If `git apply` complains about paths, try:

```powershell
git apply --directory=. "$env:TEMP\speak-replies.patch"
```

Then install deps and run from **this fork** (not only AppData):

```powershell
$env:Path = "$env:LOCALAPPDATA\Programs\Python\Python313;$env:LOCALAPPDATA\Programs\Python\Python313\Scripts;C:\Users\aiden\.local\bin;$env:Path"
uv sync --extra desktop --extra server --group desktop-native
uv pip install kokoro soundfile
```

Update `C:\Users\aiden\.openjarvis\config.toml` — add/merge:

```toml
[server]
host = "127.0.0.1"
port = 8000

[speech]
backend = "faster-whisper"
model = "base"
device = "cpu"
compute_type = "int8"
tts_backend = "kokoro"
tts_voice_id = "am_adam"
tts_speed = 1.0

[intelligence]
default_model = "qwen3.5:2b"
```

Stop any old `jarvis serve`, then start from the patched fork:

```powershell
cd C:\Users\aiden\Documents\AI-Projects\OpenJarvis
uv run jarvis serve
```

Startup should mention `TTS: kokoro`.

### Important: use the patched chat UI

The installed desktop `.exe` does **not** include this new Settings toggle until rebuilt.
For now, run the browser UI from the patched frontend:

```powershell
cd C:\Users\aiden\Documents\AI-Projects\OpenJarvis\frontend
npm install
npm run dev
```

Open the URL it prints (usually `http://localhost:5173`).

1. Settings → **Speak replies** → ON
2. Confirm **TTS backend** = Available
3. Send a short chat message
4. Jarvis should answer in text **and** speak aloud

## Undo

```powershell
cd C:\Users\aiden\Documents\AI-Projects\OpenJarvis
git checkout -- .
git clean -fd
```

Or reset to last good commit if you committed the patch.
