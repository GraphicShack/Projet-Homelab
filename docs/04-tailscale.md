# Tailscale Remote Access

## 🇫🇷 Français

Tailscale sert à accéder au homelab à distance sans ouvrir directement Samba ou les interfaces de management sur Internet.

## Problèmes rencontrés

### 1. `tailscaled` ne voulait pas partir dans le LXC

**Symptôme :** Tailscale s’installait, mais le service échouait et le socket dans `/run/tailscale/` n’existait pas.

**Ce que ça voulait dire :** Tailscale a besoin d’un TUN device, qui n’est pas toujours disponible automatiquement dans un LXC.

**Fix :** passage de `/dev/net/tun` du host Proxmox vers le container, puis restart.

**Ce que j’ai retenu :** installer un package ne veut pas dire que toutes les dépendances kernel/device nécessaires sont disponibles dans le container.

### 2. Samba à distance demandait plus que juste installer Tailscale

**Symptôme :** le Tailscale IP répondait, mais SMB restait parfois inaccessible.

**Ce que ça voulait dire :** Samba devait aussi écouter sur les bonnes interfaces et autoriser la range Tailscale.

**Fix :** vérification du port 445 et ajustement des règles Samba.

### 3. Les IP locales ne fonctionnent pas magiquement hors du réseau

**Symptôme :** les adresses `192.168.x.x` n’étaient pas accessibles depuis un autre réseau.

**Ce que ça voulait dire :** ces IP existent seulement dans le LAN de la maison. Tailscale donne son propre réseau privé, sauf si on configure du subnet routing.

**Fix :** utilisation de l’adresse Tailscale du serveur pour l’accès direct. Le subnet routing reste une amélioration prévue pour accéder à tout le LAN.

## Vérification de l’accès à distance

![Sanitized Tailscale remote access test](../screenshots/tailscale-remote-access-redacted.jpg)

Ce test a été fait à partir d’un autre réseau. Les réponses ICMP confirmaient que le tunnel Tailscale fonctionnait de bout en bout. L’adresse Tailscale et l’identité du shell sont cachées sur la capture publique.

> Les vraies Tailscale IPs et informations d’authentification ne sont pas publiées.

---

## 🇬🇧 English

# Tailscale Remote Access

Tailscale provides private remote access without exposing SMB or management ports directly to the public Internet.

## Problems encountered

### 1. `tailscaled` would not start inside the LXC

**Symptom:** Tailscale installed successfully, but the service failed to start and the socket under `/run/tailscale/` was missing.

**What it meant:** Tailscale needs a TUN network device. In an LXC, that device is not always available automatically.

**Fix:** Passed `/dev/net/tun` from the Proxmox host into the container and restarted the service/container.

**Lesson learned:** A package being installed does not mean the underlying kernel/device requirements are available inside a container.

### 2. Remote Samba access required more than just installing Tailscale

**Symptom:** The Tailscale IP could be reached, but SMB access was still inconsistent.

**What it meant:** The file service also needed to listen on the correct interfaces and allow the Tailscale address range.

**Fix:** Verified that Samba listened on port 445 and adjusted Samba network allow rules as needed.

### 3. Local IP addresses do not automatically work from outside the home network

**Symptom:** Addresses such as `192.168.x.x` were unreachable from the university network.

**What it meant:** Private LAN addresses only exist inside the home network. Tailscale provides separate private addresses unless subnet routing is configured.

**Fix:** Used the server's Tailscale address for direct access. Subnet routing was identified as the next step for reaching the entire home LAN remotely.

## Remote-access verification

![Sanitized Tailscale remote access test](../screenshots/tailscale-remote-access-redacted.jpg)

This test was performed from another network. The successful ICMP replies confirmed that the Tailscale tunnel was working end-to-end. The Tailscale address and shell identity are redacted in the public screenshot.

> Real Tailscale IP addresses and authentication details are not published.
