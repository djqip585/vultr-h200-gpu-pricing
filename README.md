# Vultr H200: Is It Available Yet? What It Costs, How It Compares to GH200 and H100, and What to Deploy Instead

Vultr publishes no H200 price, configuration, or availability on its public site. The two NVIDIA GPUs you can actually price there today are the GH200 Grace Hopper Superchip, from **$1.99/hour**, and HGX H100, listed as **“Starting at $2.300 / Per hour”**. The H200 name does appear in the URL of Vultr's Hopper product page, but the spec table and price block on that page cover H100 SXM only.

So the question worth answering is which of the GPUs Vultr will provision fits your model, and what each one costs per hour. Below is everything orderable with a published rate, why the H200 trail goes cold, and how GH200, H100 and AMD's MI325X compare for inference and multi-node training.

> **The short version**
> - **Inference on a large model:** GH200. $1.99/hour starting, 96GB HBM3 plus 480GB LPDDR5X behind a 900GB/s coherent link, so weights and context can stay on one device.
> - **Multi-node training:** HGX H100. 80GB HBM3, Quantum-2 InfiniBand clusters, Slurm or Kubernetes scheduling.
> - **Need 141GB HBM3e specifically:** check the deploy screen before you plan around it. Nothing public confirms H200 capacity.

👉 [See Vultr's current GPU catalog and new-account credit](https://bit.ly/VuLtr)

## What Vultr lists with a price right now

| GPU | Memory and bandwidth | Listed price | Best for | Deploy |
| --- | --- | --- | --- | --- |
| NVIDIA GH200 Grace Hopper | 96GB HBM3 at 4TB/s, plus 480GB LPDDR5X at up to 500GB/s; NVLink-C2C 900GB/s | **Starting at $1.99/hour** | Single-device inference, RAG, long context, memory-bound workloads | [GH200 product page](https://www.vultr.com/products/cloud-gpu/nvidia-gh200?ref=9738262-9J) |
| NVIDIA HGX H100 SXM | 80GB HBM3 at 3.35TB/s; NVLink 900GB/s; PCIe Gen5 128GB/s | **“Starting at $2.300 / Per hour”** (Vultr's formatting) | Multi-node training, standard CUDA Hopper stack, InfiniBand clusters | [H100 product page](https://www.vultr.com/products/cloud-gpu/nvidia-h100-h200?ref=9738262-9J) |
| NVIDIA H200 | 141GB HBM3e at 4.8TB/s (third-party specs only) | No public Vultr rate | Workloads that genuinely need more than 96GB per GPU | Not publicly orderable; check inside the console |

That is the whole list of NVIDIA SKUs with a published hourly rate. Vultr's wider GPU catalog also includes A100, L40S, A40 and A16, plus AMD Instinct parts, but the H200 has no price attached anywhere on the site.

## Why the H200 trail goes cold

Vultr's Hopper page sits at a URL containing both model numbers, `nvidia-h100-h200`. Everything on it refers to H100 SXM: FP64 at 34 TFLOPS, FP8 at 3,958 TFLOPS with sparsity, 80GB HBM3 at 3.35TB/s, NVLink at 900GB/s. The H200 never appears in the body text.

The only H200 figures in circulation come from trade press. DataCenterDynamics, in an article dated 14 May 2026, described the HGX H200 boards as 141GB of HBM3e with 4.8TB/s of bandwidth, offered in four-way and eight-way server configurations, then wrote that Vultr “is set to deploy H200 instances starting next year.” With that page date, “next year” would mean 2027, which contradicts the same article treating the H200 as a shipping NVIDIA product. The sentence reads like text carried over from an earlier version of the story.

Which leaves the availability question genuinely open. Nobody outside Vultr can currently say whether H200 capacity exists, is waitlisted, or is limited to one region. Treat any quoted Vultr H200 hourly rate you find elsewhere as unverified.

The check takes about two minutes: sign in, open the Cloud GPU deployment screen, and look at the model list for your target region. If H200 isn't selectable, it isn't self-serve yet. For contract capacity, ask sales for a written quote with a delivery date.

👉 [Open Vultr's H100/H200 page to see what is actually documented](https://www.vultr.com/products/cloud-gpu/nvidia-h100-h200?ref=9738262-9J)

## GH200: the option most H200 searchers should price first

People search for the H200 because they want more memory per GPU. The GH200 solves a related problem differently, by putting a CPU and a GPU in one package with a coherent link between them. Vultr's spec table lists:

- 72 Arm Neoverse V2 cores on the Grace CPU
- 480GB LPDDR5X system memory at up to 500GB/s
- 96GB HBM3 on the Hopper GPU at 4TB/s
- NVLink-C2C at 900GB/s total, 450GB/s in each direction

The interesting number is the 480GB. Vultr's FAQ describes a single memory address space covering both the LPDDR5X and the HBM3, so a model can spill past 96GB without being split across multiple GPUs or aggressively offloaded. For retrieval-augmented generation, Vultr's own description has the Grace CPU handling lookups against the vector database while the Hopper GPU runs inference, with the coherent link keeping the handoff cheap.

NVIDIA and Vultr both claim up to 10x higher performance for applications working on terabytes of data, and up to 7x the CPU-to-GPU bandwidth of a PCIe Gen5 accelerated system. Those are vendor figures, not independent benchmarks; the 7x claim rests on comparing 900GB/s of NVLink-C2C against 128GB/s of PCIe Gen5.

Two spec traps are worth knowing before you budget.

The GH200 product page's FAQ says “up to 600GB/s NVLink-C2C” in one place. The spec table and Vultr's own November 2023 launch post both say 900GB/s. Go with 900.

Trade coverage of the “updated GH200” quotes 144 Arm Neoverse cores and 282GB of HBM3e per server. That is a two-superchip server configuration. Vultr's product page sells a single superchip with 96GB HBM3 and 480GB LPDDR5X. Do not assume a single GH200 instance gives you 282GB of GPU memory.

👉 [See the GH200 spec sheet and $1.99/hour starting price](https://www.vultr.com/products/cloud-gpu/nvidia-gh200?ref=9738262-9J)

## H100: still the default for distributed training

If your job is fine-tuning or pre-training across many GPUs, the H100 is the more conventional choice. 80GB of HBM3 per GPU, 3.35TB/s of memory bandwidth, NVLink at 900GB/s inside a node, and a software ecosystem that every training framework already targets. Vultr's FAQ confirms on-demand H100 instances and multi-GPU clusters across nodes.

The cluster side is where Vultr differentiates itself from single-GPU rental shops. Vultr Clusters is self-service, runs on both Cloud GPU and Bare Metal, and Vultr states plainly that there are no reservations or special requests required. You provision through the Console or API, add high-performance storage, monitor cluster metrics, and schedule with Slurm or Kubernetes. H100 clusters are connected over NVIDIA Quantum-2 at 3200Gb/s in a non-blocking InfiniBand design.

Vultr's H100 page currently states 34 cloud data center locations across six continents; press coverage from May said 32. Either way, “30-plus regions” is the safe way to describe it. The platform also states coverage under ISO and SOC 2+ frameworks, which matters if procurement asks.

On price, quote the page as it renders: “Starting at $2.300 / Per hour.” Vultr uses three decimal places here, and the listing says “starting at,” so confirm at checkout whether the figure is per GPU-hour, whether it is on-demand or tied to committed terms, and what a full node costs. Don't build a budget from the string alone.

👉 [Compare H100 cluster options on Vultr](https://www.vultr.com/products/cloud-gpu/nvidia-h100-h200?ref=9738262-9J)

## AMD MI325X: 256GB per GPU, Chicago only

Vultr announced on 12 November 2026 that AMD Instinct MI325X GPUs are deployed in its Chicago region, and claims to be the first cloud provider offering them. The cards carry 256GB of HBM3E at 6TB/s, built on CDNA 3, running in Supermicro AS-8126GS-TNMR 8U servers, compatible with MI300X workloads and the ROCm stack.

AMD's own comparison puts the MI325X at 1.8x the memory capacity, 1.3x the bandwidth and 1.3x the peak FP16/FP8 compute of NVIDIA's H200. Those are AMD's numbers.

The trade-off is software and geography. One region means latency and data-residency constraints if your users are elsewhere, and ROCm is not CUDA. If your stack is already built around CUDA kernels and NVIDIA libraries, moving to MI325X is a project, not a config change. If you need the largest memory footprint per GPU and your code already runs on ROCm, it is the capacity play on Vultr today.

## What a month actually costs

Hourly billing is the whole point here, so the arithmetic matters. A GH200 at $1.99/hour running continuously for 30 days comes to **$1,432.80**. Run it 8 hours a day and the same month costs about $478. Nothing forces you to leave an instance on.

For the H100, do the math only after you confirm the rate on the page and at checkout. The displayed string is ambiguous enough that guessing produces numbers off by an order of magnitude.

Vultr Clusters bills without reservations and, in Vultr's phrasing, without paying for overprovisioning, so cluster cost tracks what you actually provision rather than a pre-purchased block.

One more thing to verify in your own account: the $300 code credit advertised on Vultr's landing page. The eligibility rules (new customers only, minimum spend, expiry, stacking with other offers) are not published on the pages reviewed for this article, so treat it as a possible discount and confirm the terms before you count it against your first invoice.

👉 [Check current GPU pricing on Vultr before you commit](https://bit.ly/VuLtr)

## FAQ

**Does Vultr offer H200 instances?**
No public price, spec sheet, or availability statement exists on vultr.com. The model is not confirmed either way. Check the Cloud GPU deployment screen in your account, or request a quote from sales.

**What is the difference between H200 and GH200?**
The H200 is a GPU with 141GB of HBM3e and 4.8TB/s of bandwidth per third-party reporting. The GH200 is a superchip pairing a 72-core Grace CPU with a Hopper GPU, giving 96GB of HBM3 plus 480GB of LPDDR5X in one coherent address space. One maximizes GPU memory bandwidth; the other maximizes the total memory a single device can address.

**Is a GH200 one GPU or two?**
One Hopper GPU and one Grace CPU per superchip, with 96GB of GPU memory. Multi-GPU configurations over NVLink are supported according to Vultr's FAQ, but the 282GB figure quoted in some press coverage refers to a two-superchip server.

**Is billing hourly or monthly?**
Hourly, on-demand, with the ability to stop instances. Vultr Clusters requires no reservation.

**How many Vultr locations are there?**
Vultr's own H100 page says 34 across six continents. Some press coverage says 32. Write “30-plus” if you need a number you can defend.

## Pick by workload

**Serving a model larger than 80GB, or running RAG with a long context:** GH200 at $1.99/hour. The 480GB of LPDDR5X behind a coherent link is the reason, and it costs less per hour than the H100 listing.

**Training across nodes, or running a stack that assumes CUDA and InfiniBand:** HGX H100 with Vultr Clusters. Confirm the per-GPU rate and node configuration at checkout.

**Absolutely need 141GB of HBM3e in one GPU:** verify H200 availability inside the console before you write it into an architecture doc. If it isn't there, GH200 covers most of the same memory-pressure problems, and MI325X covers the rest if you can live in Chicago and in ROCm.

Before you deploy, do two things: check the actual hourly rate on the deploy screen for your region, and read the $300 credit terms in your account. Those two numbers decide whether the bill matches the plan.

👉 [Start with GH200 at $1.99/hour on Vultr](https://www.vultr.com/products/cloud-gpu/nvidia-gh200?ref=9738262-9J)
