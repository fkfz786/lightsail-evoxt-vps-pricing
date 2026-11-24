# AWS Lightsail alternative: is the $3.50 plan really cheaper, which $5–6 VPS actually matches it, and when you should pick something else

Lightsail's $3.50 Linux plan is IPv6-only. The moment your server needs a public IPv4 address, the real entry price is $5/month for 512 MB of RAM, 20 GB of SSD and 1 TB of transfer. A plain VPS from Evoxt covers the same job with four times the memory: VM-1 gives you 2 GB of RAM, 20 GB of storage and 1 TB of monthly transfer for $5.99. If 500 GB of transfer and 5 GB of disk are enough, VM-0.5 at $2.99 does what Lightsail's entry bundle does for about 40% less per month.

What follows is the arithmetic behind that comparison, every Evoxt plan with its price, the specific points where Lightsail stops fitting, and the cases where you should not buy Evoxt at all.

> **Quick pick:** most people moving off a small Lightsail instance should take **VM-1 at $5.99/month** (1 core, 2 GB RAM, 20 GB storage, 1 TB transfer, weekly offsite backup included). It matches Lightsail's $5 tier on disk and bandwidth and doubles the RAM for 99 cents more.
> 👉 [See the VM-1 plan and current pricing](https://bit.ly/EvoXt)

## Every Evoxt plan, with prices

Evoxt sells one product line: general-purpose KVM virtual machines with root access, billed monthly up to a three-year prepayment. These are the plans on its pricing page today, all in USD, all with a weekly offsite backup at no extra charge.

| Plan | vCPU / RAM | Storage | Monthly transfer | Price | Order |
| --- | --- | --- | --- | --- | --- |
| VM-0.5 | 1 core / 512 MB | 5 GB | 500 GB | $2.99/mo | [ Deploy VM-0.5](https://bit.ly/EvoXt) |
| VM-0.75 | 1 core / 1 GB | 10 GB | 750 GB | $4.99/mo | [ Deploy VM-0.75](https://bit.ly/EvoXt) |
| VM-1 | 1 core / 2 GB | 20 GB | 1 TB | $5.99/mo | [ Deploy VM-1](https://bit.ly/EvoXt) |
| VM-1.5 | 2 cores / 2 GB | 20 GB | 1.5 TB | $6.95/mo | [ Deploy VM-1.5](https://bit.ly/EvoXt) |
| VM-2 | 2 cores / 4 GB | 30 GB | 2 TB | $11.99/mo | [ Deploy VM-2](https://bit.ly/EvoXt) |
| VM-3 | 4 cores / 4 GB | 30 GB | 3 TB | $14.99/mo | [ Deploy VM-3](https://bit.ly/EvoXt) |
| VM-4 | 4 cores / 8 GB | 60 GB | 4 TB | $23.99/mo | [ Deploy VM-4](https://bit.ly/EvoXt) |
| VM-6 | 8 cores / 8 GB | 60 GB | 5 TB | $29.99/mo | [ Deploy VM-6](https://bit.ly/EvoXt) |
| VM-8 | 8 cores / 16 GB | 80 GB | 6 TB | $47.99/mo | [ Deploy VM-8](https://bit.ly/EvoXt) |
| VM-12 | 16 cores / 16 GB | 80 GB | 8 TB | $60.95/mo | [ Deploy VM-12](https://bit.ly/EvoXt) |
| VM-16 | 16 cores / 32 GB | 100 GB | 10 TB | $95.99/mo | [ Deploy VM-16](https://bit.ly/EvoXt) |

Every plan runs on CPUs Evoxt advertises at up to 6.0 GHz single-core clock speed. Take that as a vendor claim: VPSBenchmarks records the CPU model as hidden on Evoxt's platform, so the only way to know what you got is to benchmark your own instance. Extra IPv4 addresses cost $3/month, IPv6 is supported, and there is no hourly billing, so a VM is a monthly commitment rather than something you spin up for forty minutes.

## What Lightsail's $3.50 plan actually is

Lightsail's cheapest Linux bundle is $3.50/month and it comes without a public IPv4 address. That rules it out for anything that has to answer requests from networks that don't speak IPv6, which in practice is most mail servers, a lot of corporate networks and a fair number of API clients. AWS's own pricing page puts the IPv4 entry point at $5/month with 512 MB of memory, and the spec listing for that plan splits the 512 MB across two burstable vCPUs.

The bundle model is where the bill grows. Container services start at $7/month, managed databases at $15/month and load balancers at $18/month, each as a separate package. One container service with a database behind a load balancer is $40/month before you have added a single byte of extra compute, and you are still running inside a set of fixed shapes rather than picking the RAM and disk you need.

## Where Lightsail stops fitting

Four failures show up repeatedly among people who move off it.

**Bandwidth overage.** Each bundle includes a transfer allowance, and once you pass it AWS charges data-transfer rates that are high relative to what a VPS host charges for the same gigabyte. A site that gets one traffic spike, or a service that ships backups off-site nightly, can blow past 1 TB without any change in code.

**Vertical scaling means migrating.** Growing a Lightsail instance usually involves snapshotting it and rebuilding on a larger plan. There is no slider. The snapshot can be exported to EC2 as an AMI and an EBS snapshot, which is a genuine escape hatch, but it is still a rebuild rather than a resize.

**High-clock, high-CPU work is expensive.** Persistent AI agent loops, local LLM inference and build servers all want fast single cores held for hours. Lightsail's pricing assumes bursty web workloads, so a machine that actually pins its CPU costs more than a dedicated vCPU elsewhere.

**The ecosystem sits outside the product.** SQS, Fargate autoscaling and the RDS feature set all live beyond Lightsail's boundary. A backend that grows into queues, autoscaling containers or read replicas has to leave the flat monthly bundle behind and move to EC2, where compute, EBS, data transfer and every public IPv4 address are billed separately.

There is also no free tier. Google Compute Engine and Oracle Cloud both offer one; Lightsail starts charging from 512 MB.

## Evoxt against Lightsail, line by line

|  | Evoxt VM-1 | Lightsail $5 plan |
| --- | --- | --- |
| Price | $5.99/mo | $5/mo |
| RAM | 2 GB | 512 MB |
| vCPU | 1 core, vendor-rated up to 6.0 GHz | 2 burstable vCPUs |
| Storage | 20 GB | 20 GB SSD |
| Transfer | 1 TB/mo | 1 TB/mo |
| IPv4 | additional addresses $3/mo | included at this tier, absent on the $3.50 tier |
| Backups | weekly offsite, included on every plan | snapshots you configure yourself |
| Scaling | add individual resources or upgrade from the admin console | move to a bigger bundle |
| Managed database | none | $15/mo add-on |
| Control panel | Evoxt's own panel, REST API, browser VNC, firewall rules, VM cloning | Lightsail console |

The resource line most people miss is scaling. Evoxt lets you add RAM, disk or CPU to an existing VM without switching plans, which removes the snapshot-and-rebuild step that makes Lightsail upgrades painful. The two things you give up are the managed database bundle and the AWS service catalogue behind it.

## The first-year numbers

Lightsail's $5 plan costs $60 over twelve months. Evoxt's VM-1 costs $71.88, so you pay $11.88 more per year and get 1.5 GB of additional RAM for it. That is the whole trade at the entry level.

Go down to VM-0.5 at $2.99 and the year costs $35.88, a saving of $24.12 against Lightsail. The catch is written in the table above: 5 GB of disk and 500 GB of transfer instead of 20 GB and 1 TB. A WordPress install with a couple of years of uploads, or a Docker host with three images and a log volume, will hit 5 GB faster than you expect, and once you exceed the transfer allowance you are dealing with a host that has suspended VMs for it. VM-0.5 is a genuine Lightsail replacement only for a static site, a small API or a single bot.

The honest middle is VM-1. If you know your monthly egress sits between 500 GB and 1 TB, the $3/month you save by choosing VM-0.5 is not worth the risk of being cut off mid-month.

👉 [Start with VM-0.5 for a one-month test if your transfer stays under 500 GB](https://bit.ly/EvoXt)

## What Evoxt does not do

Evoxt sells virtual machines. There is no managed PostgreSQL or MySQL line, no object storage product and no load-balancer-as-a-service. If your app expects a database with a standby node and point-in-time recovery, Lightsail's $15/month managed database or a DigitalOcean managed cluster is the right purchase and Evoxt is the wrong one. You can run Postgres on the VM yourself; you will also be the one restoring it at 2 a.m.

No hourly billing means no disposable instances. Batch jobs and short-lived test environments fit AWS or DigitalOcean better.

VPSBenchmarks lists DDoS protection and reverse DNS as missing from Evoxt's feature set, and the CPU model as hidden. The custom panel covers monitoring charts for CPU, bandwidth, RAM and storage, firewall rules, IP reassignment between servers, cloning and browser VNC, and there is a REST API at api.evoxt.com for automation.

Reviews are thin and mixed. On Trustpilot, one user describes a refund handled quickly in 2025 and says they would come back; another reports leftover account credits being non-refundable; a third describes a VM suspended for exceeding bandwidth and a two-day wait before purchased extra bandwidth was activated. Read that as a reason to size your transfer allowance with headroom rather than buy the cheapest plan and hope.

On locations, the pricing page currently lists Hong Kong and Japan (Osaka) as premium network regions, while VPSBenchmarks counted 18 data centers across four continents in August 2026. Confirm the region you need during checkout, because latency is the one thing you cannot fix after deployment.

👉 [Compare all eleven plans in the Evoxt console](https://bit.ly/EvoXt)

## When another host is the better answer

**DigitalOcean** is the closest like-for-like swap for the Lightsail experience. Droplets start around $4/month, shared and dedicated CPU sizes run to 48 vCPUs, managed databases cover PostgreSQL, MySQL, Valkey, MongoDB, Kafka and OpenSearch with standby nodes, read-only nodes and point-in-time recovery, Spaces gives you S3-compatible object storage, and Volumes resize without a reboot. Pick it when the managed services are the point, not the VM.

**Hetzner Cloud** wins on hardware per dollar. Shared and dedicated vCPU instances in Germany, Finland, Ashburn, Hillsboro and Singapore come with large traffic allowances and egress priced far below AWS. For a workload that genuinely pins CPU, this is the cheaper machine.

**Vultr** covers more than 30 locations and sells High Frequency, Cloud Compute and GPU instances, including fractional NVIDIA GPUs, alongside managed PostgreSQL, MySQL, Valkey and Kafka. Choose it when you need a server in a specific country or a cheap GPU.

**Hostinger** puts more RAM and NVMe storage at the $3.50–$5 entry point than Lightsail does and ships preinstalled panels such as CyberPanel or aaPanel. **Kamatera** lets you slide each resource independently, so 1 vCPU with 16 GB of RAM is a valid configuration, and offers a 30-day trial.

None of these are wrong answers. They are different answers, and each one costs more per gigabyte of RAM than Evoxt at the low end.

## Moving off Lightsail without downtime

1. Snapshot or back up the data and configuration on the Lightsail instance.
2. Order the Evoxt plan in the region closest to your users, with the same OS family, and add your SSH key during deployment. Evoxt advertises the VM as ready within about five minutes of payment.
3. Copy the data across with rsync or your backup tool, then reinstall the applications or containers. Root access means Docker, nginx and your process manager install normally; there is no app marketplace doing it for you.
4. Point DNS records or IP references at the new server. Evoxt's panel lets you swap and reassign IP addresses between servers, which helps when you are cutting over a production host and a failover host at the same time.
5. Test the site, mail and any cron jobs end to end.
6. Cancel Lightsail once the new machine has run clean for a few days.

## FAQ

**Is Lightsail's $3.50 plan cheaper than Evoxt's $2.99 plan?**
Only on paper. The Lightsail tier is IPv6-only, so anything that needs a public IPv4 address costs $5/month. Evoxt's VM-0.5 is $2.99 with IPv6 support and additional IPv4 addresses at $3/month, but it gives you 5 GB of disk and 500 GB of transfer against Lightsail's 20 GB and 1 TB.

**Can I run WordPress or Docker on Evoxt?**
Yes. You get root on a Linux VM, so WordPress, Docker, Coolify, n8n and similar stacks install the usual way. Evoxt's templates also cover Windows with RDP. What you don't get is a managed application platform that handles deployments, TLS and rollbacks.

**Will the price jump after the first term?**
The pricing page lists a single monthly price per plan with no separate introductory and renewal rates, and billing runs from monthly up to three years. Prepaying locks the rate for the term you buy.

**Is there a refund or a free trial?**
No blanket money-back guarantee is published on the pricing page. Reviews describe both a fast refund and non-refundable account credits, so the cheap way to test the platform is one month of VM-0.5 at $2.99 rather than a long prepayment.

**Can I upgrade without rebuilding the server?**
Yes. You can add individual resources to an existing VM or upgrade from the admin console, which is the main structural difference from Lightsail's snapshot-and-migrate path.

**What happens if I exceed the monthly transfer?**
Overage handling isn't spelled out on the pricing page, and one reviewer's VM was suspended after exceeding its bandwidth. Buy one tier above what you measured on Lightsail.

## Picking your plan

A single small site, a bot or a static front end with under 500 GB of monthly traffic: **VM-0.5 at $2.99**. Nothing above it earns its price at that workload.

A WordPress install with plugins and uploads, a small API with a database on the same box, or a Docker host running two or three containers: **VM-1 at $5.99**. The 20 GB disk and 1 TB transfer match what you had on Lightsail, and 2 GB of RAM is the difference between a site that survives a traffic spike and one that swaps.

Multiple containers plus build jobs or a busy queue worker: **VM-2 at $11.99**, which gives 2 cores, 4 GB and 2 TB.

You need a managed database with failover, object storage or a load balancer as a service: buy that from Lightsail, DigitalOcean or Vultr. Evoxt sells VMs and does that one thing without bundling anything you didn't ask for.

Before you order, check two numbers from your current Lightsail bill: peak monthly egress and actual disk usage. Those two decide between VM-0.5, VM-1 and VM-2 far more reliably than any benchmark chart.

👉 [Pick your plan and deploy in the Evoxt console](https://bit.ly/EvoXt)
