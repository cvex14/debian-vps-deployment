# Debian VPS: Debian 12 or 13, What It Should Cost, and Five Questions to Ask Before You Deploy

Install Debian 13 (Trixie). It has been Debian's stable release since August 2025 on kernel 6.12, while Debian 12 (Bookworm) left full security support in June 2026 and now runs on LTS fixes until June 2028. On price, a usable Debian box at Vultr starts at **$3.50/month for 512 MB** and **$5.00/month for 1 GB of RAM with 25 GB SSD and 1 TB of transfer**.

What decides whether that box stays cheap is which release you pin and what you confirm with the provider before you pay. Both are covered below, along with the plan table and the security steps that a minimal Debian install does not do for you.

> **Most readers should take the $5 plan.** 1 vCPU, 1 GB RAM, 25 GB SSD, 1 TB bandwidth, **$5.00/month or $0.007/hour**, listed in 31 locations. That covers Nginx plus a small Postgres, a Docker host running two or three containers, or a WireGuard endpoint. Drop to the $2.50 or $3.50 tier only if you know the workload fits in 512 MB, and move up to $10 when you're actually hitting memory limits rather than guessing. 👉 [Deploy a Debian instance on Vultr](https://bit.ly/VuLtr)

## Vultr's entry plans, side by side

These are the Cloud Compute (Regular) plans at the low end, which is where nearly every first Debian VPS lands. Vultr's catalogue also includes High Frequency, VX1, Optimized Cloud Compute, Bare Metal and GPU lines; none of those make sense for a Debian web server or a VPN endpoint, so they're left out rather than padded in with numbers I haven't confirmed.

| Plan | vCPU / RAM | SSD | Bandwidth | Price | Deploy |
| --- | --- | --- | --- | --- | --- |
| `vc2-1c-0.5gb-free` | 1 / 512 MB | 10 GB | — | **$0** | [Free tier](https://bit.ly/VuLtr) |
| `vc2-1c-0.5gb-v6` | 1 / 512 MB | 10 GB | 512 GB | **$2.50/mo** ($0.003/hr) | [IPv6 plan](https://bit.ly/VuLtr) |
| `vc2-1c-0.5gb` | 1 / 512 MB | 10 GB | 512 GB | **$3.50/mo** ($0.005/hr) | [512 MB plan](https://bit.ly/VuLtr) |
| `vc2-1c-1gb` | 1 / 1 GB | 25 GB | 1 TB | **$5.00/mo** ($0.007/hr) | [1 GB plan](https://bit.ly/VuLtr) |
| `vc2-1c-2gb` | 1 / 2 GB | 55 GB | 2 TB | **$10.00/mo** ($0.014/hr) | [2 GB plan](https://bit.ly/VuLtr) |
| `vc2-2c-2gb` | 2 / 2 GB | 65 GB | 3 TB | **$15.00/mo** ($0.021/hr) | [2 vCPU plan](https://bit.ly/VuLtr) |

Four details in that table are easy to miss and change which row you pick:

- **The $3.50 plan is Newark-only.** One location. If you need a European or APAC node, your real starting price is $5.
- **The $2.50 plan is IPv6-only and limited to Newark and Atlanta.** Fine if your traffic is IPv6-reachable or you're fronting it with a CDN; otherwise you'll spend the difference on a workaround.
- **São Paulo costs more.** The 1 GB plan bills $7.50/month there and the 2 GB plan $15.00, against $5 and $10 elsewhere. Budget for the region, not the sticker price.
- **The $0 tier exists but isn't infrastructure.** 512 MB, 10 GB, Seattle, Frankfurt and Miami. Treat it as a disposable test box for a Debian install, not something to host a client on.

Hourly billing on every row means you can deploy, break something, rebuild and pay cents. A box you keep for two days costs about $0.34 at the $5 rate.

## Debian 12 or Debian 13?

Debian 13, for anything new. Debian ships roughly three years of full security support per release and then hands the release to the LTS team for about two more years, so a version stays patched for around five years total. Bookworm has used up the first phase and is living on the second.

|  | Debian 12 (Bookworm) | Debian 13 (Trixie) |
| --- | --- | --- |
| Kernel | 6.1 | 6.12 |
| Full security support | Ended June 2026 | Roughly three years from August 2025 |
| LTS (security fixes only) | Until June 2028 | Roughly through 2030 |
| Fits | Existing installs, stacks pinned by a vendor or panel | New deployments |

The LTS phase is not a full support phase. It delivers security fixes, not the broader bug and hardware work that happens during the main cycle. A Bookworm box you deploy this month gets roughly eighteen months of that reduced service and then nothing, which is a strange lifespan to choose for a server you just bought.

Debian also freezes package versions for the life of a release. Once Trixie was frozen, the packages in it stopped moving, so an `apt upgrade` next year won't quietly swap a library version out from under your application. That's the actual product you're buying when you pick Debian over a faster-moving distribution: a base that behaves the same in month twelve as it did in month one.

Which Debian images a host offers is also a decent read on how they run their platform. Providers that track releases have had Trixie on the menu since late 2025. Others still top out at Bookworm in their configurator and say newer releases will be added when they're ready. That's not a scandal, but it tells you how much lag to expect the next time a Debian release lands. It takes thirty seconds to check before you order, and it's more informative than the CPU model in the marketing copy.

I could not confirm which Debian images Vultr currently lists. Check the OS selector in the deploy screen and make sure Trixie is there before you commit; if it isn't, Debian 12 is still a defensible choice for an eighteen-month project, just not for a long-lived one.

## Debian or Ubuntu?

They share APT, dpkg and systemd, so day-to-day administration is the same on both. The difference is cadence. Debian releases slowly, changes little between releases, and installs with almost nothing running. Ubuntu ships newer packages on a fixed schedule and backs it with a commercial support organisation.

So: pick Debian if you want the most conservative base you can get, you're comfortable reading documentation, and you'd rather patch less often. Pick Ubuntu if you need a package version Debian won't have for another two years, or you want a vendor to call when something breaks at 2 a.m. Neither is the better operating system; they're tuned for different tolerances for change.

For the workloads people actually put on a $5 VPS, Debian is the lower-maintenance answer. A minimal Debian install carries no bundled extras, so more of your 1 GB of RAM goes to Nginx or Postgres instead of a desktop-adjacent service stack you never asked for.

## Five questions to ask before you pay

**1. Which Debian release is on the deploy list today?** Not what the site's blog says, not what the comparison page from 2024 says. The image list in the order form.

**2. Is unattended-upgrades installed and configured?** On a minimal Debian install it usually isn't. Security patches then wait for you to run `apt upgrade` manually, and on a box you touch twice a month, that's a patch gap measured in weeks. Ask, or install it yourself on day one.

**3. What do backups and snapshots cost?** At most providers they're an add-on or a separate tier, not part of the plan price. A $5 server with a $3 backup line item is an $8 server, and you should know that before you compare it against anything else.

**4. Where does the provider's responsibility stop?** Managed plans generally cover the OS layer: updates, kernel, security hardening, sometimes monitoring. Application management usually stays with you even on a managed plan, so "managed" can mean less than it sounds. Get the boundary in writing, item by item.

**5. Do you get real root, and can you boot a rescue image?** Root is table stakes for a Debian VPS, but rescue mode, custom ISO upload and the ability to change the kernel are what save you when a boot config goes wrong at 1 a.m. on a Sunday.

Vultr bills hourly, so questions 1, 3 and 5 can be answered empirically: deploy the $5 plan, check the image list, look at what the backup add-on costs in the panel, and destroy it if the answers are wrong. Two hours of that costs about $0.02. 👉 [Check Vultr's current plan list and pricing](https://bit.ly/VuLtr)

## The security baseline a fresh Debian box doesn't have

Buying a Debian VPS doesn't make it secure. Debian's defaults are conservative, and the rest depends on configuration and on somebody actually maintaining it. Nine items cover most of the gap:

1. SSH keys instead of password-only login
2. Regular package updates, automated where possible
3. A firewall, default-deny inbound
4. Unnecessary services disabled
5. Root login restricted, sudo used instead
6. Strong per-user permissions
7. Backups that you have actually restored from once
8. Monitoring on CPU, memory, disk, logs and uptime
9. A review of the application and database config, not just the OS

Item 2 is the one people skip, because it requires knowing that the minimal install didn't include it.

## The $300 credit, and what it actually covers

Vultr's coupons page currently lists four offers, all new-customer only, all restricted to select products, none combinable:

| Code | Offer |
| --- | --- |
| `FLY300VULTR` | $300 free credit |
| `250VULTRFLY` | $250 free credit |
| `FLYTWOHUNDRED` | $200 free credit |
| `VULTRMATCH` | Dollar-for-dollar match on your first deposit, up to $100 |

Take the $300 code unless something in your region blocks it; there's no reason to leave $100 on the table. What the coupons page doesn't state is how long the credit lasts. Write-ups around this offer report roughly 30 days from the date it lands, which would make it a month-long test budget rather than a free quarter. Check the expiry date in Billing → Credits the moment it appears, and plan your migration test, load test or GPU experiment inside that window. 👉 [See the current Vultr credit codes](https://www.vultr.com/coupons?ref=9738262-9J)

The credit doesn't change which plan to buy. It changes how much you can afford to break while finding out.

## FAQ

**Can you run Docker, k3s or Podman on a Debian VPS?** Yes. Debian is one of the most common Docker hosts and base images, and Docker's official repository, k3s and Podman all install cleanly. On 1 GB of RAM expect two or three small containers; 2 GB is more comfortable.

**Can you upgrade Debian in place instead of rebuilding?** Yes. The standard procedure works between adjacent releases: back up, update, `apt full-upgrade` against the new release's sources, reboot, then follow the release notes for the manual steps. Rebuilding from scratch is cleaner if the box is disposable anyway.

**How do backports work if you need a newer package?** Enable the backports repository for your release, then install only the specific packages you need from it. Pulling everything from backports defeats the point of running stable.

**Is Debian 13 mature enough to deploy?** It has been the release Debian calls stable since August 2025, with kernel 6.12. Anything still calling Bookworm the current stable version is working from an outdated page.

**Does 512 MB actually work?** For a static site, a WireGuard endpoint, a small reverse proxy or a monitoring agent, yes. For a database plus a control panel plus a PHP stack, no. Memory is the constraint you hit first, and swapping on a $3.50 VPS is not a good experience.

## Which plan to pick

- Static site, WireGuard, small proxy, cron jobs: **$3.50** if Newark works, **$5** otherwise
- One application with a small Postgres or MariaDB: **$5**, move to **$10** when swap usage becomes routine
- Docker host with several containers, or a staging environment: **$10–$15**
- Anything a client depends on: add the backup line item, then compare prices

Two things to confirm at checkout: that the Debian version in the image list is 13, and what the backup add-on costs for the plan you chose. Those two answers change the real monthly number more than the difference between the $5 and $10 tiers. 👉 [Start with the $5 Debian plan on Vultr](https://bit.ly/VuLtr)
