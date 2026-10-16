# Homarr

## 🇨🇦 Français (Québec)

Homarr sert de dashboard central pour les services du homelab.

## Problèmes rencontrés

### 1. Le LXC Homarr avait temporairement plusieurs IPv4

**Symptôme :** `ip -br addr` affichait plus d’une IPv4 sur la même interface.

**Ce que ça voulait dire :** il y avait de l’adressage dynamique qui se chevauchait, donc c’était moins clair quelle IP utiliser.

**Approche :** utilisation de l’adresse qui fonctionnait pour continuer le setup, avec l’idée de nettoyer l’adressage ou faire une reservation plus tard.

### 2. L’intégration Proxmox échouait à cause du certificat TLS

**Symptôme :** Homarr retournait une erreur comme `UNABLE_TO_VERIFY_LEAF_SIGNATURE`.

**Ce que ça voulait dire :** Proxmox utilise un certificat signé par sa propre CA interne, que Homarr ne trust pas par défaut.

**Fix :** export du Proxmox root CA certificate puis ajout dans Homarr.

**Ce que j’ai retenu :** mieux vaut réparer la trust chain correctement que désactiver la validation TLS juste pour que ça marche.

### 3. Ensuite, l’intégration échouait avec une erreur d’autorisation

**Symptôme :** après avoir réglé TLS, Homarr arrivait à joindre Proxmox mais recevait une authorization error.

**Ce que ça voulait dire :** la connectivité et le certificat étaient maintenant corrects, mais le API token n’avait pas assez de permissions.

**Fix :** création d’un user/token Proxmox dédié avec le rôle read-only `PVEAuditor`.

**Ce que j’ai retenu :** authentication et authorization sont deux choses différentes. Un token valide peut quand même ne pas avoir le droit de lire une ressource.

## Troubleshooting de l’autorisation

![Sanitized Homarr Proxmox authorization troubleshooting](../screenshots/homarr-proxmox-authorization-redacted.jpg)

À ce moment-là, TLS était déjà réglé. L’erreur restante pointait donc clairement vers les permissions du API token. Les informations sensibles sont masquées.

---

## 🇬🇧 English

# Homarr

Homarr provides a centralized dashboard for homelab services.

## Problems encountered

### 1. The Homarr LXC temporarily had multiple IPv4 addresses

**Symptom:** `ip -br addr` showed more than one IPv4 address on the same interface.

**What it meant:** The container had overlapping/dynamic addressing information, which can make it unclear which address should be used.

**Approach:** Used the working address for initial setup and identified IP cleanup/static reservation as a later networking improvement.

### 2. Proxmox integration failed because the TLS certificate was not trusted

**Symptom:** Homarr reported a certificate error such as `UNABLE_TO_VERIFY_LEAF_SIGNATURE`.

**What it meant:** Proxmox uses a certificate issued by its own internal CA. Homarr did not trust that CA automatically.

**Fix:** Exported the Proxmox root CA certificate and imported/trusted it in Homarr.

**Lesson learned:** Do not disable TLS verification just to make an integration work. Trust the correct CA instead.

### 3. Proxmox integration then failed with an authorization error

**Symptom:** After fixing TLS, Homarr could reach Proxmox but returned an authorization error.

**What it meant:** Connectivity and certificate trust were now correct, but the API account/token did not have the required permissions.

**Fix:** Created a dedicated Proxmox API user/token and granted read-only `PVEAuditor` permissions.

**Lesson learned:** Authentication and authorization are separate. A valid token can still fail if it lacks permission to read the requested resources.

## Authorization troubleshooting

![Sanitized Homarr Proxmox authorization troubleshooting](../screenshots/homarr-proxmox-authorization-redacted.jpg)

At this point, certificate trust was already fixed. The remaining error therefore narrowed the problem down to API authorization. The Proxmox URL, user, token ID, and API secret are blacked out in the public screenshot.
