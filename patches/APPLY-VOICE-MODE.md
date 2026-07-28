# Apply: Always-listening Voice Mode

Requires the previous speak-replies patch already applied.

## What this adds

- **Voice Mode** button in the chat UI
- While on: mic stays open and listens continuously
- Speak → Jarvis answers out loud → listens again
- Talk over Jarvis to **interrupt** him

## Apply on Windows

```powershell
cd C:\Users\aiden\Documents\AI-Projects\OpenJarvis

Invoke-WebRequest -Uri "https://raw.githubusercontent.com/aidencole/J.A.R.V.I.S%2E/cursor/local-kokoro-speak-replies-6f09/patches/0002-voice-mode-always-listen.patch" -OutFile "$env:TEMP\voice-mode.patch" -UseBasicParsing

Get-Item "$env:TEMP\voice-mode.patch"
git apply "$env:TEMP\voice-mode.patch"
git status
```

If the branch URL 404s, use:

```powershell
$api = "https://api.github.com/repos/aidencole/J.A.R.V.I.S%2E/contents/patches/0002-voice-mode-always-listen.patch?ref=cursor/local-kokoro-speak-replies-6f09"
$obj = Invoke-RestMethod -Uri $api -Headers @{ "User-Agent" = "OpenJarvis-Setup" }
$bytes = [Convert]::FromBase64String(($obj.content -replace "\s",""))
[System.IO.File]::WriteAllBytes("$env:TEMP\voice-mode.patch", $bytes)
git apply "$env:TEMP\voice-mode.patch"
```

Then restart UI:

1. Keep `uv run jarvis serve` running from the OpenJarvis folder
2. Restart frontend:

```powershell
cd C:\Users\aiden\Documents\AI-Projects\OpenJarvis\frontend
npm run dev
```

3. Open the browser URL
4. Click **Voice Mode**
5. Allow microphone if asked
6. Speak naturally
7. To interrupt: talk over him
8. To stop: click Voice Mode again

## Notes

- Voice Mode is always listening **only while the button is on**
- It does not listen with the app closed
- Wake-word “Jarvis…” is still a later step
- First replies can still be slow on CPU; the conversation loop is the main upgrade here
