# MetroVPN for Android

Official release hub for **MetroVPN** (`site.metrodens.vpn`) — fast, battery-friendly VPN client for **Metro Dens**.

---

## 📱 Download APK (`v1.4.2`)

Go to the **[Latest Release (v1.4.2)](https://github.com/Metro-dens/MetroVPN-for-Android/releases/tag/v1.4.2)** and pick the APK for your Android device (**Android 6.0+ & Android TV**):

| Phone Type | File to Download | Size |
|---|---|---|
| **Most Android Phones (2018–2026) — Recommended** | `app-arm64-v8a-release.apk` | ~15.8 MB |
| **Older / Budget 32-bit Android Phones** | `app-armeabi-v7a-release.apk` | ~16.4 MB |
| **Not sure which phone you have?** | `app-universal-release.apk` | ~27.5 MB |

---

## ✨ What's New in `v1.4.2`

- **Accurate Speed Test Upload & High-Speed Download Engine:**
  - **4x Server-Acknowledged HTTP/1.1 Upload:** Uses 4 parallel HTTP/1.1 POST chunk streams (128 KB initial, adaptive up to 1 MB) and counts bytes strictly after `200 OK` server acknowledgement, discarding the first 1.3s warm-up window, driving the live gauge via a 1s sliding window, and computing final Upload Mbps from steady-state confirmed bytes / duration.
  - **Instant Socket Cleanup & Idle Battery Fix:** Immediately aborts all active sockets with `SO_LINGER(true, 0)` on completion, Stop, or ViewModel teardown, cleans up `SSLSocket` on TLS handshake/hostname verification failures, and stops the idle 60fps `withFrameMillis` loop when the gauge is idle.
  - **8x Parallel Download & Warm Keep-Alive Ping/Jitter:** Keeps 8 isolated TCP download streams, warm keep-alive latency/jitter probes, and a 0–1000+ Mbps gauge curve.
- **Android 8+ VPN Service & Error Lifecycle Hardening:**
  - Synchronously satisfies `startForegroundService()` on `ACTION_RESTART_HOTSPOT` and null-profile `ACTION_START` before stopping, displays `"MetroVPN Connecting…"` while handshaking, clears active profile/foreground state on stop, preserves `VpnStatus.ERROR` across failed tunnel `onDestroy()`, and splits UI banner vs VPN error snackbars so stale VPN errors never shadow app snackbars.
- **Xray Routing, Delay Tests & Multi-Protocol Fixes:**
  - Passes active `MetroSettings` into delay tests and merges ping/speed-test latencies atomically; restricts `tlshello` fragment outbound/`dialerProxy` to TCP TLS/Reality profiles (skipping Shadowsocks and UDP `kcp`/`quic`); sanitizes comma/wildcard VCN before HTTP `Host` headers; enforces RFC1918 direct rules whenever Hotspot Sharing is enabled; defaults VMess TLS SNI to single host when blank; URL-decodes Shadowsocks `method%3Apassword` credentials; live-reconnects VPN when tunnel settings change; stops VPN if the active profile is deleted; and clears stale update APK paths if signature verification rejects them.
- **QR, Share-to-Import, Updater, Storage & UI Polish:**
  - Synchronizes ZXing `MultiFormatReader`, downscales QR bitmaps > 1440px, and offloads gallery QR decoding to `Dispatchers.IO` with bitmap recycling.
  - Adds Android Share (`ACTION_SEND`) support for `.mv` files and config text plus `ClipData` URI permissions when sharing `.mv` files.
  - Hardens `PacHttpServer` (`reuseAddress = true`, `finally` reset, exception isolation), `AppUpdateChecker` (requires real `.apk` asset, Android TV safe installer launch, validated redirects), and `ProfileStorage` (self-healing invalidated AndroidKeyStore keys).
  - Offloads Hotspot IP scanning off the Main Thread, adds direct one-tap server switching on HomeScreen (`Active: <server> • Tap to switch to <selected>`), fixes protocol/import/Google DNS (`8.8.8.8`) labels and empty-filter tap-to-clear on ServersScreen, skips redundant `NO_PROXY` quota passes after clean VPN responses, and adds an **Open Source Licenses** notice in **Settings → About**.

---

## ✨ Previously in `v1.4.1`

- **High-Speed Multi-Connection Speed Test Engine:** Upgraded to 8 parallel isolated TCP download streams with warm keep-alive ping/jitter, 1.5s slow-start warmup discard, steady-state trimming, and a `0 → 1000+ Mbps` gauge curve.

---

## ✨ Previously in `v1.4.0`

- **Video Call Camera Conflict Fix:** Fixed a CameraX lifecycle leak where opening/closing the QR scanner kept the device camera locked to `MainActivity` until the MetroVPN app was closed. MetroVPN now immediately unbinds CameraX on scan, dialog close, `onPause`, and `onStop`, and detects active video-call cameras (`CameraManager.AvailabilityCallback`) so ongoing video calls never get blocked.

---

## ✨ Previously in `v1.3.9`

- **Universal Haptics on Every Android Device:** Switched hardware vibrator pulses to `USAGE_MEDIA` with multi-motor `VibratorManager` fallback and full ERM/LRA drive amplitude so tactile haptics fire reliably across Samsung, Xiaomi MIUI/HyperOS, Oppo/Realme, Vivo, Motorola, and Pixel even when system Touch Feedback is off.
- **VLESS TLS SNI Auto-Verification Fix:** Automatically populates `verifyPeerCertByName` (`address,sni`) for domain-based VLESS TLS links that use spoofed ISP SNIs (`sni=st1.zoom.us`) without an explicit `&vcn=` parameter, fixing TLS handshake failures.
- **Full Multi-Protocol & Multi-Transport Support:** Added support for **VMess (`vmess://`)**, **Trojan (`trojan://`)**, and **Shadowsocks (`ss://`)** alongside **VLESS (`vless://`)**, plus **TCP+HTTP, WebSocket, gRPC, XHTTP, SplitHTTP, HTTPUpgrade, H2, mKCP, and QUIC**.

---

## ✨ Previously in `v1.3.8`

- **Hotspot Share & Android TV 11 Fixes:**
  - Fixed HTTP proxy half-close stalls (`uplinkOnly: 2`, `downlinkOnly: 5`, `handshake: 8`, `connIdle: 300`) that caused Android TV Wi-Fi Manual Proxy to hang on "Connecting...".
  - Fixed SOCKS5 UDP relay (`socks-in` now binds and advertises the real Hotspot LAN IP instead of `127.0.0.1`) so full-tunnel UDP/DNS/QUIC works on Android TV via SOCKS5 (`:10808`).
  - Switched Xray routing `domainStrategy` to `AsIs` and removed `; DIRECT` PAC fallback to eliminate carrier DNS stalls and silent fallback onto normal Mobile SIM data.
  - Added instant Hotspot Gateway IP auto-detection (excluding cellular/IMS/P2P interfaces like `pdp0`, `seth_lte0`, `epdg0`, `p2p-wlan0-0`), serialized FGS-safe Hotspot proxy restarts, Android 13+ LAN `excludeRoute`, and live **`PROXY READY`** status verification in Settings.
  - Added native **Android TV** launcher & feature compatibility (`LEANBACK_LAUNCHER`, non-required touchscreen) plus an in-app **Android TV Setup (Stop SIM Data Leak)** guide.

---

## ✨ Previously in `v1.3.7`

- **Security Hardening & Vulnerability Audit Fixes:**
  - Verified downloaded update APK package ID, monotonic `versionCode`, and signing certificate SHA-256 against the installed app, plus HTTPS GitHub-only allowlist for update URLs and redirects.
  - Encrypted saved VPN profiles (`profiles_json`) at rest with Android Keystore AES-256-GCM (auto-migrating existing profiles) and added Android 12+ `dataExtractionRules` / `fullBackupContent` backup exclusions.
  - Fixed Android 6.0/7.0 (`minSdk 23`) crashes (`Base64` & `contentLengthLong` `[NewApi]` Lint errors resolved to 0 errors).
  - Hardened Hotspot PAC HTTP server against JS injection and header/thread DoS, blocked SSRF private/loopback/metadata IPs & redirects in quota lookups, and redacted UUIDs/keys from in-app logs.

---

## ✨ Previously in `v1.3.6`

- **Redesigned Glowing Wavy Speedometer & Scalloped Medallion UI:** 12-lobe Material 3 Expressive scalloped medallion, glowing 28-cycle flowing sine-wave arc (`0 → 500 Mbps` logarithmic scale) with 6° split-gap inactive track, leading tip dot, active-phase metric card highlights, and live **Test Data Cost** breakdown (`↓ MB · ↑ MB`).
- **Ookla / Fast.com-Grade Multi-Stream Speed Test:** 8-sample unloaded ping, RFC 3550 jitter, and packet loss % probes, 4-stream parallel download saturation with trimmed outlier filtering and concurrent **Loaded Ping** measurement, plus 3-stream real HTTP `POST` upload throughput testing.
- **Data Quota Sync Fix & Auto-Refresh:** Fixed Data Quota syncing over active VPN tunnels (via proxy/direct fallback) and IP/VCN server nodes, plus automatic quota refresh right after connecting.
- **Cancellable In-App Auto-Update & Installer:** Proxy-aware in-app APK downloader with live progress bar, **Cancel**, and **Browser** fallback controls.
- **Xiaomi MIUI / HyperOS & Android 13+ Haptics Fix:** Added `FLAG_IGNORE_GLOBAL_SETTING`, `VibrationAttributes.USAGE_TOUCH` (Android 13+), and `AudioAttributes.USAGE_ASSISTANCE_SONIFICATION` (Android 8–12) plus full haptic triggers across hero toggle, Settings rows, switches, navigation bar, and server swipe cards.
- **Settings About Tab & Telegram Links:** Expandable **About** tab above **Build Version & Updates** with direct links for **@MetroVpnPaidProof** (Channel) and **@Metrodens_bot** (Bot).
- **Material 3 Expressive + Slide-Right Data Quota + Hotspot Share:** Shape-morphing hero toggle, Hotspot Share in Settings, swipe right on server cards for Data Quota, compact QR camera + Gallery upload, Airtel & Mobitel filters, and Android 6.0+ (`minSdk 23`) support.

---

## 🛡️ Install Guide (Play Protect)

When installing directly outside the Play Store, Android may show a prompt before installing:
1. Tap **Scan app** (takes ~5 seconds to verify).
2. Tap **Install**.

---

## 💬 Official Channels

- Bot & VIP Plans: **[@Metrodens_bot](https://t.me/Metrodens_bot)**
- Updates & Notices: **[@METRODENSVPN](https://t.me/METRODENSVPN)**
- Speed Proofs: **[@MetroVpnPaidProof](https://t.me/MetroVpnPaidProof)**
