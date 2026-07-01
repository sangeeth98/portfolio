# Scratchpad: Astro Dev Server Resolution on Windows

## 🔍 Core Issue Identified

1. **Astro 7 Automatic Background Mode**:
   - In Astro 7, the CLI automatically detects if it is running inside an AI coding agent environment and defaults to starting the development server in `--background` mode.
   - It outputs a JSON state block like `{"message":"Dev server running at ... (pid ...)"}` and immediately exits the parent terminal process with code 0.

2. **Windows Job Tree Process Termination**:
   - On Windows, when standard terminal tasks finish and their parent shell exits, the OS's job management tree automatically terminates all detached child subprocesses spawned during that task lifecycle.
   - As a result, the detached Astro background dev server process was being silently killed by Windows immediately after Astro printed the success message.

## 🛠️ Permanent Fix

To run the Astro dev server reliably under an AI agent session on Windows, we must force Astro to run in the **foreground** and stay alive inside the active task channel. We achieve this by setting the following environment variable:

```powershell
$env:ASTRO_DEV_BACKGROUND=0; npx astro dev
```

This disables background mode detection, keeping the dev server active and listening on port `4321`.

## 🧪 Verification Results

- Tested the endpoint locally: `Invoke-WebRequest -Uri "http://localhost:4321"` returned **StatusCode 200 OK**.
- Dev server is fully active and watching for file changes.
