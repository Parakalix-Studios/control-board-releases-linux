# Control Board for Linux - releases

Linux builds and signed updates for **Control Board**, a native desktop app
that watches a Proxmox homelab: hosts, guests, graphs, a network map,
Kubernetes, web checks, certificates, and alerts. The source is private; this
repository holds only builds. Windows builds are in
[control-board-releases](https://github.com/Parakalix/control-board-releases).

**Status: private alpha.** Proprietary software: by installing it you accept
the [licence](LICENSE).

## What you need

- A 64-bit desktop Linux with WebKitGTK 4.1 (Ubuntu 22.04 or newer, Debian 12
  or newer, or similar)
- A system keyring (GNOME Keyring or KWallet) for the secrets you add
- A Proxmox VE host or cluster that your computer can reach
- Optional: a Kubernetes cluster, websites to check, SSH access to the hosts

## What it connects to

Everything runs on your computer. The app talks directly from your computer
to the systems you add, and to nothing else of ours:

- your Proxmox, Kubernetes, hosts and web checks, with the read-only access
  you give it
- Cloudflare's API, only if you add a Cloudflare token
- GitHub, to check this repository for updates

There is no telemetry, no account and no server in the middle. Your settings,
history and secrets stay on your computer.

## Install

Download the newest build from [Releases](../../releases/latest), either:

- **AppImage** (any distribution): make it executable and run it:

  ```
  chmod +x Control.Board_<version>_amd64.AppImage
  ./Control.Board_<version>_amd64.AppImage
  ```

- **.deb** (Debian, Ubuntu and their derivatives):

  ```
  sudo apt install ./Control.Board_<version>_amd64.deb
  ```

Control Board opens on **Nothing is set up yet**.

## Set it up

Everything is added from **Sources > Add a source**. Each source is tested
before it can be saved: it must answer, and it must be **unable to change
anything**. Secrets go into your system keyring, never into a file. If no
keyring is running, the app says so instead of saving the secret.

**Proxmox** (start here). On any node, as root, make a read-only token:

```
pveum user add board@pve --comment "Control Board"
pveum aclmod / -user board@pve -role PVEAuditor
pveum user token add board@pve ro --privsep 0
```

The last command prints the secret once. In the app enter a name, the API
address (`https://<node-ip>:8006`), the token ID `board@pve!ro` and the
secret, then **Test** and **Save**. Hosts and guests appear within seconds.

**Host access (SSH)**, optional: lets the board read failed services, ZFS
pools, pending updates and backups on each host. The app makes its own key and
shows the line to add to `/root/.ssh/authorized_keys` (on a Proxmox cluster
that file is shared, so once per cluster). Replace `<your LAN>` in it with
your network, e.g. `192.168.1.0/24`. Without it, the host probe stays off.

**Kubernetes** and **web addresses**, optional: a read-only kubeconfig (list
and watch, no Secrets), or any URL to check every 20 seconds.

Guest **roles** come from Proxmox tags you choose. The settings file
(`~/.config/com.parakalix.controlboard/board.yaml`, with comments) says what
a stopped guest with each role raises, and holds anything the wizard doesn't
cover.

## Updates

New versions appear in the title bar. Click it, then **Install & restart**.
Every update is signed, and the app refuses one whose signature does not
match.

## Uninstall

Delete the AppImage, or `sudo apt remove control-board` for the .deb. Your
settings and history stay in `~/.config/com.parakalix.controlboard`, and the
secrets stay in your keyring under the service name `Control Board`. Delete
both for a clean slate.

## Found a bug?

Open an [issue](../../issues) with your distribution and version, what you
did, what you expected and what happened; a screenshot helps. Please leave
out passwords, tokens, addresses and anything else private: issues here are
public.
