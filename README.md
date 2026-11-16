# H100 Cloud Pricing: What $2.30 to $4.59 an Hour Buys, SXM vs PCIe, and Where You Can Start One Today (Vultr, Together, Lambda and Runpod Compared)

H100 cloud rental currently spans **$2.30 to $4.59 per GPU-hour**. Vultr lists its HGX H100 at a starting rate of **$2.300/hour**, Runpod quotes **$2.89** for a PCIe card and **$4.39** for SXM, Together charges **$3.99** on demand for SXM with reserved rates down to **$3.45**, and Lambda sits at **$4.59**.

Most of that spread comes from the hardware and the contract terms rather than from discounts. The cheapest listings are usually PCIe cards, preemptible capacity, or a "starting at" figure attached to a prepaid commitment. Paying $4+ normally buys an SXM card on NVLink with clear per-minute billing. Knowing which one you are renting matters more than shaving a dollar off the hourly rate.

> **Quick pick:** need an H100 node on demand, no reservation, lowest published starting rate, and non-blocking InfiniBand across 34 locations, look at Vultr's HGX H100. Need a written SXM spec plus a published reserved-rate ladder for a multi-week run, Together's $3.99/$3.45 tiers are easier to plan against.
>
> 👉 [Check Vultr's HGX H100 rate and availability](https://www.vultr.com/products/cloud-gpu/nvidia-h100/?ref=9738262-9J)

## Vultr's H100 and the rest of its GPU catalog

Vultr's H100 product is the **NVIDIA HGX H100** in SXM form, 80GB of HBM3 per GPU, with FP8 support and NVIDIA Quantum-2 3200Gb/s InfiniBand on a non-blocking fabric. The published price is **$2.300 per GPU-hour**, labeled "Starting at" on the product page.

| GPU | Memory / form factor | Price per GPU-hour | What the price covers | Order |
| --- | --- | --- | --- | --- |
| NVIDIA HGX H100 | 80GB HBM3, SXM | **$2.300** (starting at) | 8-GPU node; 27 vCPU and 239.25GB RAM per GPU share | [View H100 plan](https://www.vultr.com/products/cloud-gpu/nvidia-h100/?ref=9738262-9J) |
| NVIDIA HGX B200 | 192GB, SXM | $3.50 | 8-GPU node; 31 vCPU and 353.25GB RAM per GPU share | [Check Vultr GPU plans](https://bit.ly/VuLtr) |
| NVIDIA HGX A100 | 80GB, SXM | $2.800 (starting at) | 8-GPU node | [Check Vultr GPU plans](https://bit.ly/VuLtr) |
| NVIDIA A100 | 80GB, PCIe | $2.40 | Single-GPU plan, 12 vCPU, 120GB RAM, 1.40TB storage, 10TB bandwidth | [Check Vultr GPU plans](https://bit.ly/VuLtr) |
| NVIDIA L40S | 48GB, PCIe | **$1.671** | Single-GPU plan, 16 vCPU, 180GB RAM, 1.20TB storage, 10TB bandwidth | [View L40S plan](https://www.vultr.com/products/cloud-gpu/nvidia-l40s/?ref=9738262-9J) |
| NVIDIA A40 | 48GB, PCIe | $1.71 | Single-GPU plan, 24 vCPU, 120GB RAM, 1.40TB storage, 15TB bandwidth | [Check Vultr GPU plans](https://bit.ly/VuLtr) |

Vultr's Cloud GPU catalog is bigger than that table, and the console is the only complete source for it. The rows above are the configurations with a per-hour figure I could verify: the H100 and L40S numbers come from Vultr's own product pages, the A100, A40 and B200 figures from a pricing snapshot of Vultr's site taken on 17 October 2026. Vultr does not publish a minimum billing unit, a storage rate, or an egress rate on the H100 page, so those need confirmation at the point of order.

## The $2.30 question: which H100 are you actually renting?

Three shipping variants carry the H100 name and they are not interchangeable. The SXM5 card is the one people mean when they talk about training clusters; the PCIe card is a different product with a different memory type and no NVLink.

|  | H100 SXM5 | H100 PCIe | H100 NVL |
| --- | --- | --- | --- |
| Memory | 80GB HBM3 | 80GB HBM2e | 94GB |
| Bandwidth | 3.35TB/s | ~2TB/s | not published here |
| CUDA / Tensor cores | 16,896 / 528 | 14,592 / 456 | — |
| FP8 Tensor Core | 3,958 TFLOPS | 3,026 TFLOPS | — |
| NVLink | 4.0, 900GB/s | none | — |
| TDP | 700W | 350W | — |
| MIG | up to 7 instances | up to 7 instances | — |

The bandwidth gap is 68%, and it shows up directly in throughput on large-model work. NVLink is the bigger deal for multi-GPU jobs: SXM cards get 900GB/s of GPU-to-GPU bandwidth per card, PCIe cards fall back to the standard PCIe bus, which is far slower for gradient exchange.

Three rules cover most decisions:

- **Multi-node distributed training:** SXM only. PCIe cards without NVLink will bottleneck on all-reduce long before the FLOPS do.
- **Single-GPU fine-tuning or inference:** PCIe is fine and costs less. Runpod's $2.89 vs $4.39 is the same trade at a different vendor.
- **More than 80GB of VRAM needed:** look at the 94GB NVL variant, or step up to H200 at 141GB.

Any listing that quotes a price without naming the form factor is telling you half the story. A $2.30 or $2.89 rate on a card you cannot identify is not comparable to a $4.39 SXM rate.

## What an H100 hour costs across Vultr, Runpod, Together and Lambda

On-demand H100 rates cluster into two bands: $2.89 and below for PCIe or starting-at pricing, $3.99 to $4.59 for SXM.

| Provider | Card and form factor | On-demand, per GPU-hour | Billing model | Notes |
| --- | --- | --- | --- | --- |
| Vultr | HGX H100, SXM | **$2.300** starting at | Not published on product page | 8-GPU node, 3200Gb/s non-blocking InfiniBand |
| Runpod | H100 PCIe | $2.89 | Per second | Storage billed separately |
| Runpod | H100 NVL, 94GB | $3.19 | Per second | More memory, less bandwidth than SXM |
| Runpod | H100 SXM | $4.39 | Per second | The SXM comparison point |
| Together | H100 SXM | $3.99 | Per hour | $3.69 at 7–30 days, $3.45 at 31–90 days |
| Lambda | HGX H100, SXM | $4.59 | **Per minute** | 22TiB SSD included, no egress fees |
| Runpod | Serverless H100 | $5.89 | Per request | Scales to zero when idle |

Runpod's figures are the ones shown on its own H100 guide page, which carries a March 2026 date in the body even though the headline says October 2026. Treat them as page-shown prices and re-check before committing to a long run.

Take a concrete job: **8 SXM GPUs for 100 hours, 800 GPU-hours in total**, on-demand, excluding storage and egress.

- Vultr at the $2.300 starting rate: **$1,840**
- Together at $3.99: **$3,192**; at the 31–90 day reserved rate of $3.45: **$2,760**
- Runpod SXM at $4.39: **$3,512**
- Lambda at $4.59: **$3,672**

The $1,840 figure depends entirely on that starting rate being the rate you are actually billed. Vultr labels $2.300 as "starting at," and a third-party tracker that queried Vultr's public plans API in October 2026 reports the 8-GPU HGX H100 node at $23.92/hour ($2.99 per GPU) with on-demand deployment flagged off and a preemptible rate of $18.40/hour ($2.30 per GPU). That reading points to $2.30 being preemptible or contract pricing rather than flat on-demand. Confirm the number in the console before you size a budget around it.

The second bill is storage. Runpod charges $0.07/GB/month for standard network volumes on the first terabyte and $0.14/GB/month for high-performance storage, so 1TB of checkpoints runs $70 a month. Lambda bundles 22TiB of SSD into the $4.59 instance and charges no egress. Vultr's H100 page does not publish a storage or bandwidth rate, which is one more item for the checkout page.

👉 [Compare Vultr's HGX H100 configuration and starting price](https://www.vultr.com/products/cloud-gpu/nvidia-h100/?ref=9738262-9J)

## What you get on Vultr beyond the hourly rate

Vultr's differentiator is the network and the provisioning model, not the headline price. H100 nodes sit on **NVIDIA Quantum-2 3200Gb/s InfiniBand with a non-blocking design**, which is the fabric you want when a job spans more than one node and all-reduce traffic has to cross switches.

Capacity is spread across **34 cloud data center locations on six continents**. For inference where the model is small enough for one card, running in the region nearest your users cuts round-trip time in a way no single-region cluster can match. Vultr also holds **ISO and SOC 2+** compliance, which matters if you are putting regulated data through the pipeline.

Clusters are self-service. Vultr Clusters provisions and scales HGX H100 nodes through the Console or the API, schedules with **Slurm or Kubernetes**, attaches high-performance storage, and exposes cluster metrics. The H100 page states there is no need for reservations or special requests. Vultr's L40S page uses the opposite wording, so confirm provisioning terms in the console if your schedule depends on immediate capacity.

Three gaps to check at checkout: the minimum billing increment, whether storage and bandwidth are billed on top of the $2.300, and whether any new-account credit applies to GPU hours. Vultr's signup flow sometimes carries a promotional credit for new accounts, and the eligibility rules, expiry window and product coverage only appear in the billing flow. Read them before you plan a training run around one.

👉 [Deploy an H100 cluster through Vultr Clusters](https://bit.ly/VuLtr)

## When an H100 is the wrong buy

A single 7B to 13B model serving inference does not need an H100. An A100 80GB handles it at **$2.09/hour on Runpod** or **$3.19/hour on Lambda**, and the H100's advantage (FP8 with the Transformer Engine, roughly 3–4x A100 throughput on transformer workloads) only pays off when the workload is actually compute-bound.

Consumer cards cover the small end. An RTX 4090 with 24GB runs at about $0.89/hour on Runpod and handles anything that fits under 24GB. The jump to H100 is justified by memory capacity as much as speed: 80GB of HBM3 at 3.35TB/s is roughly three times the bandwidth of an RTX 5090, and 30GB to 40GB models do not fit on a consumer card at all.

Above 80GB, the H200 is the cheaper upgrade than stacking more H100s. Together lists H200 140GB at **$5.99/hour** on demand and $4.15 reserved; Runpod lists it at **$5.29/hour**. Same GPU die, same compute, 141GB of HBM3e at around 4.8TB/s. If your model fits inside 80GB, the H200 premium buys nothing.

For commitments past a few hundred GPUs, the published reserved ladders are the only thing you can plan against. Lambda's 1-Click Clusters run 16 to 2,000+ GPUs on 2-week to 1-year terms; Together scales 8 to 4,000 GPUs and prices reserved windows in three tiers. Vultr's H100 page publishes no reserved-rate table at all.

## FAQ

**How much does an H100 cost per hour in the cloud?**
Published on-demand rates run from a $2.30 starting price on Vultr's 8-GPU HGX H100 node to $4.59 per GPU-hour at Lambda. SXM cards cluster between $3.99 and $4.59; PCIe cards sit lower, at $2.89 on Runpod.

**How much slower is a PCIe H100 than an SXM one?**
Memory bandwidth is 3.35TB/s on SXM against roughly 2TB/s on PCIe, a 68% gap. SXM adds NVLink 4.0 at 900GB/s per GPU, which PCIe cards do not support at all. SXM also carries 16,896 CUDA cores and 3,958 TFLOPS of FP8 against 14,592 cores and 3,026 TFLOPS.

**How much does reserving save?**
Together's H100 SXM drops from $3.99 on demand to $3.69 for a 7–30 day reservation (about 7.5% off) and $3.45 for 31–90 days (about 13.5% off). Beyond 91 days it goes to sales.

**How large a model fits in 80GB?**
A 70B parameter model at 8-bit precision is roughly 70GB of weights, which leaves almost nothing for KV cache, so 70B serving normally spreads across multiple GPUs or drops to lower-bit weights. 7B to 13B models fit comfortably on one card.

**Can I get an H100 on Vultr without a reservation?**
Vultr's H100 page states that clusters can be deployed at will with no reservations or special requests, through the Console or the API. Confirm the billing rate in the console, because $2.300 is published as a starting price.

## How to choose

Match the card to the job before you match the price:

- **Multi-node training, 8 cards or more, want the lowest published starting rate:** Vultr's HGX H100, with the $2.300 rate confirmed in the console first.
- **Multi-week training with a budget that needs a fixed number:** Together at $3.45 for 31–90 days, or Lambda's per-minute billing if the job is short and bursty.
- **Single-card fine-tuning or inference:** Runpod PCIe at $2.89, or an A100 80GB at $2.09 if the model is under 30B.
- **More than 80GB of VRAM:** H200 at $5.29 (Runpod) or $5.99 (Together), or the 94GB NVL at $3.19.

Two things to settle before you pay: the actual billing increment and whether storage and bandwidth are included, and whether any signup credit applies to GPU hours and when it expires. Those two answers change the real hourly cost more than a $1 difference in the headline rate.

👉 [Start with Vultr's HGX H100 and confirm the rate at checkout](https://www.vultr.com/products/cloud-gpu/nvidia-h100/?ref=9738262-9J)
