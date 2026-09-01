# 🌌 Antigravity Companion: KawaiiNeko Automation & Deployment Specifications
**Target Notebook:** Antigravity Companion Notebook
**Tags:** `#antigravity` `#agv-ide` `#remote-deployer` `#apk-scaffolder` `#shizuku-guard` `#n8n-android` `#avc-100`

This document serves as your master developer specification bible, compiling raw architectural logs, automation scripts, and native mobile-to-cloud sync patterns extracted from recent sessions. Use this file as a direct reference for your Antigravity (AGV) IDE sessions and Goal-Mode agent prompts.

---

## 1. THE REMOTE IDEA FACTORY PIPELINE [20260824-1910]

Capture raw concepts, checklists, and blueprints on the go (via Google Keep or Termux) and transmit them directly to your headless OCI Ubuntu VPS. A background daemon automatically picks them up, compiles the code inside your Antigravity IDE, runs tests, and outputs detailed execution walkthroughs.

### A. SSH/SCP Transport Protocol
Configure your local Termux/desktop SSH client to easily push files to the VPS directory `~/.antigravity/incoming_ideas/` using your pre-configured OCPkit key.

#### Step 1: Configure SSH Host (Termux / SSH Config)
Add this block once to `~/.ssh/config` so you can connect to your server with `ssh ocpkit`:
```text
Host ocpkit
    HostName <YOUR_VPS_IP_OR_LOCAL_TUNNEL_IP>
    User ubuntu
    IdentityFile ~/.ssh/id_ed25519_ocpkit
```

#### Step 2: Create the Termux Ingestion Pipe (`agv-push`)
Run this inside Termux to create a quick, executable command for piping your thoughts:
```bash
cat << 'EOF' > $PREFIX/bin/agv-push
#!/usr/bin/env bash
set -euo pipefail
# Pipes stdin over SSH to the watched prompt directory on the VPS
ssh ocpkit "cat > ~/.antigravity/incoming_ideas/prompt_\$(date +%Y%m%d_%H%M%S).md"
echo "✔ Blueprint pushed successfully to Remote Idea Factory!"
EOF
chmod +x $PREFIX/bin/agv-push
```

#### Step 3: Interactive "Cat EOF" Pipe on the Fly
Now, you can copy any blueprint from your clipboard, type `agv-push` in Termux, paste the text, and hit `Ctrl + D` on a new line to fire it off to the VPS watcher.

---

### B. Asynchronous Watcher Daemon (`agv_remote_idea_deployer.py`)
This daemon runs continuously in a Python virtual environment (`/home/ubuntu/app/venv`) on your remote VPS, checking for incoming blueprints and initializing the Google Antigravity SDK.

#### Systemd Daemon Configuration (`/etc/systemd/system/agv-idea-deployer.service`)
```ini
[Unit]
Description=Google Antigravity Remote Idea Watcher Daemon
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/app
ExecStart=/home/ubuntu/app/venv/bin/python3 /home/ubuntu/app/agv_remote_idea_deployer.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

---

## 2. SPARK DEVELOPMENT TRIGGER SKILLS

Save these specifications into your custom Spark system prompts or Custom GPT/Gems instructions.

### `/antigravity-plan` (The Implementation Scaffolder)
Generates comprehensive, zero-omission implementation specifications for scripts, plugins, and web apps inside Google Antigravity IDE. It enforces a strict 5-tier parameter checklist:
1.  **Core Feature & Logic:** Primary functional entry points, validation rules, and boundary error conditions.
2.  **State Persistence & "Sticky" Settings:** Local storage / session keys for toggles and UI filters, fallback defaults, and automatic state persistence on change.
3.  **Secondary UI Components:** FAQ help modals (Escape key dismissal, click-outside closures), Status Badges, Toast notifications, and mobile drawer transitions.
4.  **Documentation & Metadata Sync:** Pre-drafted README additions, storage schema explanations, and high-contrast SemVer changelogs.
5.  **Deployment & Sync Pipeline:** Target GitHub branch configurations, folder boundaries, and ignore files.

### `/antigravity-builder` (The Autonomous Scaffolder)
Instructs Spark to output ready-to-run file trees and zips:
*   `src/main.js` (Core logic and event loops)
*   `src/state.js` (Sticky state caching and fallback variables)
*   `src/components/faq-modal.js` (Modals with native keyboard traps)
*   `src/components/toast.js` (Atomic screen overlays)
*   `scripts/deploy-sync.sh` (Shell file for rclone/webhook deployment)
*   `README.md` & `CHANGELOG.md`

---

## 3. HYBRID MOBILE COMPILATION: `/apk-scaffolder`

This scaffold provides full-stack boilerplate structures designed for compilation via Gradle directly in your Antigravity workspace terminal to output ready-to-use Android packages (APKs).

*   **Android Manifest:** `android/app/src/main/AndroidManifest.xml` (gating Internet, external storage, and notification permissions cleanly).
*   **Capacitor Bridge:** `capacitor.config.json` (routing local-first file paths like `dist` and setting background colors).
*   **Mobile UI Container:** `src/index.html` (with deep violet `#120d18` and high-contrast `#e0d0f0` scannable themes).
*   **Local Persistence API:** `src/app.js` (local storage hooks that dump into structured `.md` files).
*   **Build Execution Pipeline (`scripts/build-apk.sh`):**
    ```bash
    #!/usr/bin/env bash
    set -euo pipefail
    npm run build
    npx cap sync
    cd android
    ./gradlew assembleDebug
    # Automatically moves and names the APK using package and build config metadata
    cp app/build/outputs/apk/debug/app-debug.apk ../builds/com.anymd.app_debug_v$(date +%Y%m%d).apk
    ```

---

## 4. VAULT SECURITY: SOFT GATING & FRONTMATTER PIN-LOCKS [20260824-1914]

True to the plain-text philosophy, Anymd implements **soft UI gating** rather than cryptographic DRM. 

*   **The Soft Gate:** Background processes, indexing, and watched-folder syncs continue running invisibly, but Anymd intercepts file clicks inside the UI and renders a PIN overlay if locked.
*   **The YAML Bypass:** Users who know Markdown can bypass the lock manually on disk by simply editing the frontmatter or config settings:
    ```yaml
    ---
    ui_guard:
      enabled: false
      pin_hash: null
    ---
    ```
*   **FOSS Safety Vault Exports:** The system exports complete database vaults into zipped AES-256 archives. Importing them triggers a visual PIN dialog requiring verification before rehydrating settings and Markdown nodes to the filesystem.

---

## 5. MOBILE SYSTEMS INTEGRATION: THE SHIZUKU PROCESS GUARDIAN

For your Android environment running on aggressive battery-killing systems (like OxygenOS), the mobile setup implements privileged ADB system access.

*   **The Shizuku Binder (`ShizukuBridge.kt`):** Executes ADB shell commands (`dumpsys`) to whitelist Anymd and local n8n containers from being killed by the Android Doze engine.
*   **The Device Vault:** Runs foreground services (Bluetooth Battery Listeners, Clipboard Watchers, and Notification Ingest services) that continuously scrape hardware telemetry and write them directly as markdown notes inside your custom `.nothere/device-vault/` local directory.

---

## 6. N8N & POSTGRESQL ON ANDROID (THE PROOT CONTAINER WORKAROUND) [20260825]

*   **The Blocker:** Modern n8n defaults to `isolated-vm` for expression sandboxing, which crashes on native Android/Termux (Bionic libc) due to glibc memory-mapping incompatibilities.
*   **The Workaround:** Run n8n inside a lightweight PRoot Linux container (Ubuntu/Debian) on Android. PRoot provides full glibc and standard V8 isolate bridge support out of the box.

#### The All-In-One Robust Launcher (`start-n8n.sh`)
This script handles the Postgres daemon lifecycle, checks socket availability, enforces the sandbox bypasses, and launches n8n with hardcoded credentials inside your Termux environment:
```bash
#!/usr/bin/env bash
# start-n8n.sh inside PRoot Ubuntu
set -e

# Disable isolated-vm and force execution inside the main node process
export N8N_EXPRESSION_EVALUATOR_PROCESS=main
export N8N_RUNNERS_ENABLED=false
export DB_TYPE=postgresdb
export DB_POSTGRESDB_HOST=127.0.0.1
export DB_POSTGRESDB_PORT=5432
export DB_POSTGRESDB_DATABASE=n8n_vault
export DB_POSTGRESDB_USER=n8n_user
export DB_POSTGRESDB_PASSWORD=n8n_pass

# Start local PostgreSQL if not running
if ! pg_isready -h 127.0.0.1 -p 5432 >/dev/null 2>&1; then
    echo "🐘 Initializing PostgreSQL cluster..."
    pg_ctl -D /var/lib/postgresql/data start
fi

echo "⚡ Starting n8n server..."
exec n8n start
```

---

## 7. THE 256-BIT VIRTUAL CONSOLE (AVC-100 STANDARD)

The system operates like a retro 8-bit/16-bit console: a single lightweight host runtime (the "Console") manages the frame buffer, audio/haptic pings, global currency/achievements, and mascot overlays, while individual games are hot-swapped as self-contained `.kat` or `.mdcart` (Markdown Cartridge) files.

### A. Technical Specifications
*   **128-Bit Score Registers:** For tracking extreme point bounds in high-speed, multi-combo score engines.
*   **24-Bit TrueColor Palette Remapping:** Remaps standard terminal color outputs dynamically into high-contrast palettes ($OUT, $FILL, $ACC, $TXT).
*   **Mobile Touch Coordinate Translation:** Map touch gestures and swipe-coordinates directly into monospace character grid cells (80 columns x 25 rows).
*   **Cryptographic High Score Verification:** Emits a signed telemetry run-validation token that can be automatically pushed to a public `LEADERBOARDS.json` repository via GitHub Actions, preventing score injection.

### B. Aesthetic Specifications (Max-Kawaii Core)
*   **The Mascot Daemon (Cat-Orb):** Displays reactive ASCII face states (e.g., `( ఠ ͟ʖ ఠ )` during intense calculation, `(✧ω✧)` during combos, and `(( ◣ ͟ʖ ◢ ))` during low-timer decay panic). It features a sinusoidally waving text tail (`~~~`) whose wag speed maps to your inputs.
*   **The Foundation Beds:** Solitaire card slots are rendered visually as cozy ASCII bunk beds with a pillow slot `[=]` and blanket fringes `[~~~~~~~~~]`. Cards are "tucked in" as you sort them, and animate/whine when left untouched too long.
*   **Cosmetic Wardrobe Matrix:** Equip the Cat-Orb with swapout hats (Pastel Cap, Crown, Rose Mask, Stonk Hat) that dynamically shift its speech generator from supportive cozy banter to mean, sarcastic roasts.
