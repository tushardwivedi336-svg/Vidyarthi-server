# Vidyarthi-server
Shri Ram Paramedical College 
# Vidyarthi Register — Server Setup (Windows)

This is a real server: it stores student records and documents as actual files on
your PC, and any computer that can reach it (on your network or over the internet)
gets the same live data.

## 1. Install Node.js (one-time)

1. Go to https://nodejs.org and download the **LTS** installer for Windows.
2. Run it, clicking "Next" through the defaults.
3. Open **Command Prompt** (search "cmd" in the Start menu) and type:
   ```
   node --version
   ```
   If it prints a version number, you're set.

## 2. Install and run the server

1. Copy this whole `server-app` folder anywhere on your PC, e.g. `C:\VidyarthiRegister`.
2. Open Command Prompt, then navigate into the folder:
   ```
   cd C:\VidyarthiRegister
   ```
3. Install the dependencies (only needed once, or after you change anything):
   ```
   npm install
   ```
4. Start the server:
   ```
   npm start
   ```
   You should see:
   ```
   Vidyarthi Register server running at http://localhost:3000
   Created default accounts:
     Registrar : admin / admin123
     Entry Desk: entrydesk / entry123
   ```
5. Open that address (`http://localhost:3000`) in a browser on the same PC to confirm it works.

**Change the default passwords immediately** — sign in, go to Manage Users → Change my password.

Keep the Command Prompt window open — closing it stops the server. To stop it on purpose, click the window and press `Ctrl+C`.

## 3. Let other computers on the same WiFi connect

1. In Command Prompt, run `ipconfig` and note the "IPv4 Address" (e.g. `192.168.1.42`).
2. On another computer on the same WiFi/network, open a browser and go to:
   ```
   http://192.168.1.42:3000
   ```
   (using your PC's actual IP address).
3. Windows Firewall may prompt you to allow Node.js on private networks the first time — click **Allow**.

This works for computers in the same office/building. For different locations, continue to step 4.

## 4. Let computers in different locations connect (Cloudflare Tunnel)

This is the safest way to reach your PC from anywhere without opening ports on
your router or exposing your home IP address.

### Quick option (a temporary public link, good for testing)

1. Download `cloudflared` for Windows from:
   https://github.com/cloudflare/cloudflared/releases (grab the `cloudflared-windows-amd64.exe` file).
2. Rename it to `cloudflared.exe` and place it somewhere simple, e.g. `C:\Cloudflared\cloudflared.exe`.
3. Open Command Prompt in that folder and run, with your server already running:
   ```
   cloudflared.exe tunnel --url http://localhost:3000
   ```
4. It will print a random web address like `https://random-words.trycloudflare.com` —
   share that with the other locations. It works as long as this command keeps running.

Quick tunnels get a new random address every time you restart them, and Cloudflare
says they're meant for testing, not long-term production use — good for trying
things out, not for daily operation with real student documents.

### Permanent option (a stable address that doesn't change)

This needs a domain name added to your free Cloudflare account (a cheap domain,
around $10–15/year, works fine — you don't need anything fancy).

1. Create a free account at https://dash.cloudflare.com and add your domain.
2. In the dashboard, go to **Networking → Tunnels → Create Tunnel**, choose Windows,
   and follow the install commands it shows you (it will walk you through downloading
   `cloudflared`, logging in, and creating the tunnel).
3. Add a **Published application route** pointing your chosen subdomain (e.g.
   `records.yourdomain.com`) at `http://localhost:3000`.
4. Optionally set the tunnel to **run as a Windows service** (Cloudflare's docs have
   a "Run as a service on Windows" guide) so it starts automatically and keeps running
   even after you restart your PC or log out.

Because sensitive documents (Aadhar, income proof, caste certificates, etc.) will be
reachable from the internet, it's worth adding **Cloudflare Access** in front of the
tunnel — it asks anyone visiting the address for an email one-time code before they
even reach your login page, which is a good extra layer beyond the app's own login.

## 5. Keep the PC available

Whichever internet option you choose, your PC needs to stay on and connected for
others to reach it. If that's not realistic for your setup (e.g. the PC gets
shut down every night, or moved between locations), a small always-on cloud
server (around $5/month from a provider like a VPS host) running this same
code is a more reliable long-term option than a personal PC.

## What's stored where

- `data/students.json` — all student records and field values
- `data/schema.json` — the current set of fields and document slots (editable from the Manage Fields screen)
- `data/users.json` — login accounts (passwords are hashed, never stored in plain text)
- `uploads/<student-id>/` — the actual uploaded documents, one folder per student

Back up the whole `server-app` folder regularly (a simple copy to an external
drive or cloud storage folder is enough) — this is where all your real data lives.
