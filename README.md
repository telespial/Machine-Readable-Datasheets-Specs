# Machine Readable Datasheet (MRD)

[← Back to EmbeddedX-Specs (canonical index)](https://github.com/telespial/EmbeddedX-Specs)

## Hardware Datasheets For AI Ingestion

Proposed by: Richard Haberkern  
Contact: rmhaberkern@gmail.com

Free for evaluation. Commercial use requires permission. See [LICENSE.md](./LICENSE.md) for more information.

Part of the **EmbeddedX specification family**.

**Canonical index:** start at `EmbeddedX-Specs`:
https://github.com/telespial/EmbeddedX-Specs

* * *

## Abstract

Machine Readable Datasheet (MRD) defines a machine-readable format for the parts of a device description that firmware actually depends on. It pulls together the details engineers usually chase across datasheets, reference manuals, and errata, then puts them in a form that tools and reviewers can reuse.

MRD focuses on firmware-relevant device data: pins, pad functions, registers, fields, interfaces, constraints, conflicts, and errata. It is not trying to replace a vendor datasheet. It is trying to capture the operational details software depends on.

* * *

## 1. Scope

MRD may describe:

* device identity and revisions
* packages, pads, and pins
* pin multiplexing rules
* registers and fields
* peripheral blocks
* clocks, resets, interrupts, DMA
* interface instances
* electrical and functional constraints
* errata and workarounds
* traceability to source documentation
* optional model-targeting feature sets and runtime constraints

* * *

Model extension details: [docs/model-feature-set.md](./docs/model-feature-set.md)

* * *

## 2. Why MRD Matters

The data firmware needs is scattered. Engineers jump between PDFs, code examples, app notes, and errata just to confirm what a device can do and what firmware is allowed to assume.

MRD establishes a reusable hardware contract so that generators and reviewers are not forced to rediscover the same rules every time.

* * *

## 3. Relationship to Other Specifications

* **EmbeddedX (umbrella):** https://github.com/telespial/EmbeddedX-Specs
* **MRC:** https://github.com/telespial/Machine-Readable-Connectivity-Specs
* **MDP:** https://github.com/telespial/Model-Definition-Package-Specs
* **EIL:** https://github.com/telespial/Embedded-Intelligence-Layer-Specs
* **AICS:** https://github.com/telespial/AI-Coding-System-Specs
* **EIP:** https://github.com/telespial/Embedded-Intelligence-Package-Specs

MRD establishes what a device or pin can do.
MRC establishes what that device or pin is actually connected to in a board design.

MRD model-targeting extensions define what model feature sets are permitted on a specific hardware target.

* * *

## License

See [LICENSE.md](./LICENSE.md)
