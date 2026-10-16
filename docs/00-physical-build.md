# Physical Build & Early Setup

## 🇨🇦 Français

Avant les containers, les reverse proxies et les dashboards, le vrai premier problème était pas mal plus simple : transformer un vieux desktop en serveur qui pouvait réellement être utilisé comme homelab.

## Point de départ

J’ai préféré recycler une vieille tour au lieu d’acheter du server hardware dédié.

Les objectifs étaient assez simples :

- réutiliser le hardware que j’avais déjà;
- installer Proxmox VE comme hypervisor;
- héberger des services Linux et des Windows lab VMs;
- ajouter du shared storage;
- réussir à connecter le serveur sans passer un nouveau câble Ethernet dans toute la maison.

## Problèmes rencontrés

### 1. Pas de connexion Ethernet pratique près du serveur

**Problème :** la tour était placée dans une pièce où je n’avais pas de câble Ethernet qui se rendait directement au router.

**Pourquoi c’était important :** un hypervisor est beaucoup plus simple à gérer avec une connexion wired stable. Le Wi-Fi directement dans Proxmox est aussi moins pratique qu’avec un OS desktop normal.

**Options que j’ai regardées :**

- USB/PCIe Wi-Fi adapter;
- Wi-Fi repeater avec sortie Ethernet;
- powerline adapters;
- éventuellement un vrai wired backhaul ou mesh backhaul.

**Solution temporaire :** utiliser un Wi-Fi repeater / wireless bridge pour donner une connexion Ethernet au serveur.

**Ce que j’ai retenu :** le réseau physique fait partie du design du serveur. Même avec une config Proxmox parfaite, un mauvais uplink rend tout plus lent ou instable.

### 2. L’adresse Proxmox n’était pas joignable au début

**Symptôme :** après l’installation, l’interface web de Proxmox était difficile ou impossible à joindre à partir d’un autre appareil.

**Ce que j’ai vérifié :**

- gateway du router;
- subnet;
- static IP choisie;
- bridge configuration;
- NIC réellement utilisée.

**Fix :** correction de la network configuration puis validation de l’accès à Proxmox sur le LAN.

**Ce que j’ai retenu :** une static IP est pratique pour l’infrastructure, mais elle doit évidemment être dans le bon subnet avec la bonne gateway.

### 3. Hardware virtualization désactivée dans le BIOS

**Symptôme :** Proxmox disait que hardware-accelerated KVM n’était pas disponible.

**Cause :** Intel VMX / VT-x était désactivé dans le BIOS.

**Fix :** activation de Intel VMX, sauvegarde, reboot.

**Résultat :** les VMs pouvaient enfin utiliser la virtualisation matérielle normalement.

### 4. Il fallait intégrer le storage récupéré

Le NVMe interne sert pour Proxmox et le storage des VMs/containers. Un external drive sert pour les fichiers.

Le drive externe en exFAT a été monté sur le host Proxmox puis passé au Ubuntu Server LXC.

**Problème :** exFAT ne gère pas les permissions Linux comme ext4 ou ZFS.

**Fix :** utilisation des bonnes options de mount avec UID/GID pour que le user dans le unprivileged LXC puisse lire et écrire.

### 5. Les performances dépendaient aussi du réseau physique

À un moment, parcourir les photos et fichiers semblait plus lent que prévu.

Le bottleneck pouvait venir du chemin réseau :

```text
Client
  -> Wi-Fi
Router
  -> Wi-Fi
Repeater / bridge
  -> Ethernet
Proxmox server
```

Chaque saut wireless peut réduire le throughput et ajouter de la latency.

**Ce que j’ai retenu :** les performances d’un homelab, c’est compute + storage + networking. Ajouter de la RAM ne règle pas un lien Wi-Fi faible.

## Layout physique final

```mermaid
flowchart LR
    Internet --> Router[Home Router]
    Router -. Wi-Fi .-> Repeater[Wi-Fi Repeater / Bridge]
    Repeater -->|Ethernet| PVE[Repurposed Desktop running Proxmox VE]
    PVE --> NVMe[Internal NVMe]
    PVE --> USB[External Storage]
```

## Pourquoi cette partie vaut la peine d’être documentée

C’est une des parties qui m’a le plus appris, parce que ce n’était pas juste du software :

- hardware reuse;
- BIOS configuration;
- physical networking;
- storage mounting;
- Linux permissions;
- diagnostic hardware vs network vs software.

Le homelab n’a pas juste été « installé en suivant un tuto » : il a fallu l’adapter à l’environnement réel où il roule.

---

## 🇬🇧 English

# Physical Build & Early Setup

Before any containers, reverse proxies, dashboards, or remote-access tooling worked, the project started with a much less glamorous problem: turning an old desktop into an actual usable homelab server.

## Starting point

The server was built from an older desktop tower that was repurposed instead of buying dedicated server hardware.

The main goals were:

- reuse existing hardware;
- install Proxmox VE as the hypervisor;
- host Linux services and Windows lab VMs;
- add shared storage;
- make the server accessible without running a new Ethernet cable through the house.

## Problems encountered

### 1. No convenient Ethernet connection near the server

**Problem:** The tower was installed in a room without a direct Ethernet run to the router.

**Why it mattered:** A hypervisor is much easier to manage when it has a stable wired connection. Wi-Fi support on Proxmox is also less straightforward than on a normal desktop operating system.

**Approach considered:**

- USB/PCIe Wi-Fi adapter;
- Wi-Fi repeater with Ethernet output;
- powerline adapters;
- eventually, a proper wired or mesh backhaul.

**Temporary solution:** Used a Wi-Fi repeater / wireless bridge arrangement to give the server Ethernet connectivity.

**Lesson learned:** The physical network is part of the server design. A perfectly configured Proxmox host is still frustrating if the uplink is unstable or slow.

### 2. Initial Proxmox network address was unreachable

**Symptom:** After installation, the Proxmox web interface could not reliably be reached from another computer.

**What was checked:**

- router gateway;
- subnet;
- chosen static IP;
- bridge configuration;
- whether the selected NIC actually had connectivity.

**Fix:** Corrected the network configuration and verified access to the Proxmox web interface from the LAN.

**Lesson learned:** A static IP is useful for infrastructure, but it must match the real LAN and gateway.

### 3. Hardware virtualization was disabled in BIOS

**Symptom:** Proxmox reported that hardware-accelerated KVM was unavailable.

**Cause:** Intel VMX / VT-x was disabled in firmware.

**Fix:** Entered the BIOS, enabled Intel VMX, saved the settings, and rebooted.

**Result:** Full virtualization support became available for VMs.

### 4. Repurposed storage needed to be integrated

The build used the internal NVMe drive for Proxmox/VM storage and an external drive for file storage.

An external exFAT drive was mounted on the Proxmox host and then passed into the Ubuntu Server LXC.

**Problem:** exFAT does not behave like a normal Linux filesystem for ownership and permissions.

**Fix:** Used mount options with the correct UID/GID mapping so the unprivileged LXC user could read and write files.

### 5. Performance depended on more than CPU and RAM

At one point, file/photo browsing felt slower than expected.

The likely bottleneck was not only the VM/container configuration but also the physical network path:

```text
Client
  -> Wi-Fi
Router
  -> Wi-Fi
Repeater / bridge
  -> Ethernet
Proxmox server
```

Every extra wireless hop can reduce throughput and increase latency.

**Lesson learned:** Homelab performance is a combination of compute, storage, and networking. Adding RAM does not fix a weak wireless uplink.

## Final physical layout

```mermaid
flowchart LR
    Internet --> Router[Home Router]
    Router -. Wi-Fi .-> Repeater[Wi-Fi Repeater / Bridge]
    Repeater -->|Ethernet| PVE[Repurposed Desktop running Proxmox VE]
    PVE --> NVMe[Internal NVMe]
    PVE --> USB[External Storage]
```

## Why this part matters

This phase ended up being one of the most useful parts of the project because it involved real infrastructure constraints:

- hardware reuse;
- BIOS configuration;
- physical networking;
- storage mounting;
- Linux permissions;
- diagnosing whether a problem was hardware, network, or software.

The homelab was not just "installed from a tutorial" — it had to be adapted to the physical environment it was actually running in.
