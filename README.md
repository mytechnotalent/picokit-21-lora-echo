![picokit-21-lora-echo](https://raw.githubusercontent.com/mytechnotalent/picokit-21-lora-echo/main/picokit-21-lora-echo.png)

<br>

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/reverse-engineering)
## FREE Embedded Hacking Course [HERE](https://github.com/mytechnotalent/Embedded-Hacking)

<br>

# PICOKIT-21 LORA ECHO

### Two Way Token Echo and Authenticated Heartbeat
#### Lesson 21 of the Picokit Series

<br>

***
**LEGAL DISCLAIMER:**
The information, tools, and code provided in this repository and course are strictly for educational, research, and defensive purposes only.

You are explicitly prohibited from using any materials contained herein to access, test, modify, or exploit any device, network, or system that you do not own 100% or for which you do not have explicit, documented, and legally binding authorization to interact with.

By using this repository and course, you acknowledge and agree that:

1. Any illegal, unauthorized, or malicious use of this information is solely your responsibility.
2. The author(s) and contributor(s) of this repository and course shall not be held liable for any damages, legal repercussions, criminal charges, or unauthorized actions resulting from the use, misuse, or abuse of the contents herein.
3. You will comply with all applicable local, state, national, and international laws regarding cybersecurity and computer fraud.

**IF YOU DO NOT AGREE WITH THESE TERMS, DO NOT USE THIS REPOSITORY AND COURSE.**
***

<br>
<br>

## Overview

The twenty first Picokit lesson. The node transmits a short authenticated
token every five seconds, the Python gateway echoes an acknowledgement back
over LoRa, and the node logs the echoed acknowledgement and carries it in its
next heartbeat. It closes the loop on the two way radio path that the earlier
lessons only used in one direction.

<br>

## What it teaches

- Sending a compact authenticated token on a fixed five second cadence.
- Receiving and parsing an echoed acknowledgement on the downlink.
- Logging the echoed acknowledgement and reporting it in the next heartbeat.
- Sealing a tiny JSON body with Argon2id and XChaCha20-Poly1305 and sending it
  with an AT+SEND over the RYLR998.

<br>

## Hardware

| Peripheral | Pico 2 pin | Role |
| --- | --- | --- |
| Onboard LED | GP25 | heartbeat, one blink per transmit |
| RYLR998 | GP8 TX / GP9 RX | token uplink and echo downlink |
| Debug Probe | SWCLK/SWDIO/GND, GP0/GP1 | SWD and the console |

<br>

## How it works

The node runs `monitor_step` in a loop. Every 5 seconds it seals
`{"n":21,"s":<seq>,"a":<ack>}` with the field key and sends the token over
LoRa. When the gateway echoes an acknowledgement, the node logs it as
`ACK <n>`, stores it, and reports it in the next heartbeat. The gateway
authenticates each frame and only then parses it.

<br>

## Build and flash

```bash
cd firmware
cmake -S . -B build -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s
cmake --build build
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg \
  -c "program build/picokit_21_lora_echo.elf verify reset exit"
```

<br>

## Watch the node

Open the console at 115200 and reset:

```text
BOOT
=== PICOKIT-21 LORA ECHO // TOKEN ECHO + AUTHENTICATED HEARTBEAT ===
RX from 0x0001, 5 bytes
ACK 7
RX from 0x0001, 5 bytes
```

<br>

## The gateway

```bash
cd gateway
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python3 listen.py --port /dev/cu.usbserial-A50285BI --hub 0001 --network 18 --db gateway.db
```

It prints `OK node=21 rssi=...` per authenticated heartbeat and echoes an ACK
back to the node. The terminal dashboard `python3 tui.py --db gateway.db` and
the web dashboard `python3 web/app.py --db gateway.db` show the same rows.

<br>

## Verify

```bash
python3 .opencode/skill/embedded-c-standard/audit_c_standard.py
python3 .opencode/skill/embedded-python-standard/audit_python_standard.py
python3 .opencode/skill/iot-readme-standard/validate_readme.py
python3 .opencode/skill/iot-banner-standard/validate_banner.py
python3 scripts/run_tests.py
python3 scripts/check_coverage.py
```

<br>

# Next
[picokit-22-lora-telemetry](https://github.com/mytechnotalent/picokit-22-lora-telemetry)

<br>

# License
[MIT License](https://github.com/mytechnotalent/picokit-21-lora-echo/blob/main/LICENSE)
