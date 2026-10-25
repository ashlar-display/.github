# Ashlar

**A display connection stack in Rust — HDMI 2.1 first.**

Ashlar takes a display link from raw capability bytes to a trained link: parse what the
sink can do, negotiate the best mode the source, sink and cable can all carry, describe it
in InfoFrames, and bring up the link over SCDC with FRL training. Each layer is its own
crate — use one, or use them all.

**Website: [ashlar-display.org](https://ashlar-display.org)** · [Getting started](https://ashlar-display.org/getting-started.html)

## The stack

```
Integration layer      compositor glue · DRM/KMS · source capability query   (planned)
Driver-adjacent layer  FRL link training                                     plumbob · plumbob-async
Protocol layer         CEC · eARC                                            (planned)
Signaling layer        InfoFrames · SCDC                                     cartouche · culvert · culvert-async
Negotiation layer      enumeration · validation · ranking                    concordance
Parsing layer          EDID · DisplayID                                      piaf
Shared types           display vocabulary                                    display-types
Hardware abstraction   SCDC transport · PHY                                  hdmi-hal · hdmi-hal-async · hdmi-hal-i2c-dev
```

## Crates

| Crate                                                                  | Layer                | What it does                                                                          |                                                                                                                 |
|------------------------------------------------------------------------|----------------------|---------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [display-types](https://github.com/ashlar-display/display-types)       | Shared types         | Display capability vocabulary shared across the stack                                 | [![crates.io](https://img.shields.io/crates/v/display-types.svg)](https://crates.io/crates/display-types)       |
| [piaf](https://github.com/ashlar-display/piaf)                         | Parsing              | EDID and DisplayID parser                                                             | [![crates.io](https://img.shields.io/crates/v/piaf.svg)](https://crates.io/crates/piaf)                         |
| [concordance](https://github.com/ashlar-display/concordance)           | Negotiation          | HDMI 2.1 mode negotiation with a full reasoning trace                                 | [![crates.io](https://img.shields.io/crates/v/concordance.svg)](https://crates.io/crates/concordance)           |
| [cartouche](https://github.com/ashlar-display/cartouche)               | Signaling            | InfoFrame encoding and decoding (AVI, Audio, HDR Static, HDMI Forum VSI, Dynamic HDR) | [![crates.io](https://img.shields.io/crates/v/cartouche.svg)](https://crates.io/crates/cartouche)               |
| [culvert](https://github.com/ashlar-display/culvert)                   | Signaling            | Typed SCDC register access                                                            | [![crates.io](https://img.shields.io/crates/v/culvert.svg)](https://crates.io/crates/culvert)                   |
| [culvert-async](https://github.com/ashlar-display/culvert-async)       | Signaling            | Async companion to culvert                                                            | [![crates.io](https://img.shields.io/crates/v/culvert-async.svg)](https://crates.io/crates/culvert-async)       |
| [plumbob](https://github.com/ashlar-display/plumbob)                   | Driver-adjacent      | FRL link training state machine                                                       | [![crates.io](https://img.shields.io/crates/v/plumbob.svg)](https://crates.io/crates/plumbob)                   |
| [plumbob-async](https://github.com/ashlar-display/plumbob-async)       | Driver-adjacent      | Async companion to plumbob                                                            | [![crates.io](https://img.shields.io/crates/v/plumbob-async.svg)](https://crates.io/crates/plumbob-async)       |
| [hdmi-hal](https://github.com/ashlar-display/hdmi-hal)                 | Hardware abstraction | `ScdcTransport` and `HdmiPhy` traits                                                  | [![crates.io](https://img.shields.io/crates/v/hdmi-hal.svg)](https://crates.io/crates/hdmi-hal)                 |
| [hdmi-hal-async](https://github.com/ashlar-display/hdmi-hal-async)     | Hardware abstraction | Async companion to hdmi-hal                                                           | [![crates.io](https://img.shields.io/crates/v/hdmi-hal-async.svg)](https://crates.io/crates/hdmi-hal-async)     |
| [hdmi-hal-i2c-dev](https://github.com/ashlar-display/hdmi-hal-i2c-dev) | Hardware abstraction | Linux userspace backend over `/dev/i2c-N`, for development and validation             | [![crates.io](https://img.shields.io/crates/v/hdmi-hal-i2c-dev.svg)](https://crates.io/crates/hdmi-hal-i2c-dev) |

## Principles

- **Hardware behind traits.** SCDC transport and PHY control are injected, so all protocol
  logic is testable without hardware.
- **Diagnostics are first-class.** Structured reasoning traces and typed warnings instead of
  logs. Rejected candidates are explained; nothing fails silently.
- **Spec-complete, judgment-free lower layers.** Parsing and protocol crates implement the
  spec in full; policy lives in the negotiation layer, and you can override it.
- **`no_std` where it matters**, async companions where they help.
- **No unsafe code.** `#![forbid(unsafe_code)]` across the stack.
- **Verifiable releases.** Trusted publishing only, with SLSA provenance.

## Beyond HDMI

EDID and DisplayID parsing, the shared display types, and CTA-861 InfoFrame payloads are not
HDMI-specific. DisplayPort (and USB-C DisplayPort Alt Mode) support is a direction the stack
is designed to grow into.

Licensed under MPL-2.0.
