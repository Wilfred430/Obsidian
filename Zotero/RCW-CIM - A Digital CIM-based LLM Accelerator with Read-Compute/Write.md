---
citekey:
  "{ citekey }":
status:
tags:
  - literature-note
---

# RCW-CIM: A Digital CIM-based LLM Accelerator with Read-Compute/Write

**Authors:** Yan-Cheng Guo, Tian-Sheuan Chang
**Year:** Error: `format` can only be applied to dates. Tried for format object
**Journal:** 
**Zotero Link:** [Open in Zotero]()

## Abstract
Digital computing-in-memory (DCIM) has emerged as a promising solution for large language model (LLM) acceleration by minimizing data transfers between external DRAM and on-chip accelerators while maintaining high precision for superior accuracy. However, existing CIM architectures often overlook weight update latency, which becomes critical as LLM weights are far larger than a single CIM macro’s capacity. To address this issue, this paper proposes a read-compute/write (RCW) architecture that effectively minimizes weight update latency, along with a nonlinear operator fusion that further mitigates dependency-induced latency. The proposed RCW reduces decoding computing latency by 21.59% on the Llama2-7B model. In addition, the nonlinear operator fusion mechanism achieves a 69.17% latency reduction through efficient partial accumulation and group-based approximation. Furthermore, a weightstationary and output-column-stationary (WS-OCS) dataflow is introduced to reduce both external DRAM access and internal CIM weight updates—by 51.6% and 87.6%, respectively—during the prefill phase of 1024 tokens, leading to an overall 49.76% latency reduction. Fabricated using TSMC 22 nm CMOS technology and operating at 100 MHz, the proposed RCW-CIM achieves 3.28 TOPS and 42.3 TOPS/W, enabling 4.2 ms prefill latency and 26.87 decoded tokens per second for the INT4-weight Llama2 model with dual DDR5-6400 memory.

---
## Zotero Notes & Highlights

> Digital computing-in-memory (DCIM) (p. 1)

---
> minimizing data transfers between external DRAM and on-chip accelerators while maintaining high precision for superior accuracy. (p. 1)

---
> existing CIM architectures often overlook weight update latency (p. 1)

---


---
> read-compute/write (RCW) architecture (p. 1)

---
> The proposed RCW reduces decoding computing latency by 21.59% on the Llama2-7B model. (p. 1)

---


---
> nonlinear operator fusion (p. 1)

---
> weightstationary and output-column-stationary (WS-OCS) dataflow (p. 1)

---
> Fabricated using TSMC 22 nm CMOS technology and operating at 100 MHz, (p. 1)

---
> To accommodate fine-tuning for diverse tasks (p. 1)

---
> massive model weights significantly increase data transfer between DRAM and accelerators (p. 1)

---
> combines computation and memory to reduce the weight transfer overhead while minimizing computational loss and preserving high precision. (p. 1)

---
