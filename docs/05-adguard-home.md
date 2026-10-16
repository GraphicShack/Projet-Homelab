# AdGuard Home

## 🇨🇦 Français

AdGuard Home fait du network-level DNS filtering.

## Problèmes rencontrés

### 1. AdGuard roulait mais montrait zéro requête DNS

**Symptôme :** le dashboard restait à 0 et les pubs étaient encore visibles.

**Ce que ça voulait dire :** AdGuard allait bien, mais le Mac/browser utilisait encore un autre DNS resolver.

**Fix :** changement du DNS du client vers AdGuard, puis validation des requêtes dans le dashboard.

**Ce que j’ai retenu :** installer un DNS server ne sert à rien tant que les clients ne l’utilisent pas réellement.

### 2. Secure DNS du browser pouvait bypass le resolver local

**Symptôme :** une partie du trafic pouvait encore éviter AdGuard même après avoir changé le DNS dans l’OS.

**Ce que ça voulait dire :** certains browsers utilisent DNS-over-HTTPS indépendamment du système.

**Fix :** vérification du réglage Secure DNS pendant les tests.

### 3. Certaines pubs restent visibles même quand AdGuard fonctionne

**Symptôme :** des pubs pouvaient encore apparaître sur Reddit ou YouTube.

**Ce que ça voulait dire :** un DNS blocker peut bloquer un domaine complet, mais pas toujours différencier une pub et du contenu normal servis à partir du même domaine.

**Ce que j’ai retenu :** DNS filtering et browser content blocking ne règlent pas exactement le même problème.

---

## 🇬🇧 English

# AdGuard Home

AdGuard Home provides network-level DNS filtering.

## Problems encountered

### 1. AdGuard was running but showed zero DNS queries

**Symptom:** The dashboard showed 0 requests and ads were still visible.

**What it meant:** AdGuard itself was healthy, but the Mac/browser was still using another DNS resolver.

**Fix:** Changed the client's DNS server to the AdGuard LXC address and verified name resolution through AdGuard.

**Lesson learned:** Installing a DNS server does nothing until clients actually use it.

### 2. Browser secure DNS could bypass the local resolver

**Symptom:** Some browsing traffic could still avoid AdGuard even after the operating system DNS was changed.

**What it meant:** Browsers can use DNS-over-HTTPS independently of the operating system.

**Fix:** Checked the browser's secure DNS configuration during testing.

### 3. Some ads still appear even when AdGuard works correctly

**Symptom:** Ads remained visible on services such as Reddit or YouTube.

**What it meant:** DNS blockers can block entire domains, but they cannot always distinguish advertising content served from the same domain as the normal site content.

**Lesson learned:** DNS filtering and browser content blocking solve different problems. AdGuard is useful, but it is not a complete replacement for a browser-level blocker.
