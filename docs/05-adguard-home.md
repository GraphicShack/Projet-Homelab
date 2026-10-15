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
