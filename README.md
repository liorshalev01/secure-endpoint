# The Secure Endpoint System

A **hardware enforced architecture for secure sessions** that verifiably
decouples user session security from the host computer's security posture.

## The problem: the untrusted endpoint

Organizations must give users access to sensitive data, but they cannot
guarantee the security of the user's computer. If the host's OS, I/O stack, or
browser is compromised, any software-based security running inside it is
compromised too. Malware reads plaintext **before** it is encrypted for
transmission (key loggers) or **after** it is decrypted for display (screen
scraping) — bypassing the cryptographic protections of the session entirely.
Existing approaches (VDI, RBI, SASE, SGX, EDR) all ultimately depend on the
integrity of that same host software stack.

## The approach

The Secure Endpoint (SE) system physically and cryptographically separates the
trusted I/O path from the untrusted host, through two interconnected
innovations:

- **Hardware-enforced I/O segregation** — a transmit-only **Secure Input Device
  (SID)** captures and encrypts user input, while a receive-only **High Security
  Side (HSS)** decrypts and renders all output. Unidirectional hardware (a
  tamper-resistant fiber-optic bridge, independent power/clocks, RF shielding,
  TPM 2.0 roots of trust) removes the physical pathways malware needs to
  intercept plaintext I/O.
- **Three-node cryptographic trust model** — a "Synchronized ACK" protocol
  establishes a synchronized session across three trusted nodes: the SID, the
  HSS, and a cloud **Secure Access Gateway (SAG)**. Two distinct unidirectional
  channels (SID→SAG for input, SAG→HSS for output) reduce the host computer and
  its browser to relays for opaque, encrypted data.

The protocol uses static-ephemeral ECDHE (NIST P-384) for forward secrecy,
AES-256-GCM authenticated encryption, monotonic sequencing for replay
resistance, and periodic key rotation — so the integrity and confidentiality of
the session are demonstrably independent of the host's security.

## Documentation

**[The Secure Endpoint System whitepaper](papers/secure-endpoint-system.md)**
covers the architecture, system components, hardware hardening, threat model,
the complete cryptographic and protocol design, the system lifecycle, and a
worked Secure VDI use case.

## Demo

**[secure-endpoint-rpi5](https://github.com/liorshalev01/secure-endpoint-rpi5)**
is a reference implementation of the trusted display node (the HSS) on a
Raspberry Pi 5. It sits between an untrusted host and its screen: the host's
HDMI output is captured, scanned for Data Matrix markers carrying AES-256-GCM
encrypted payloads, decoded and decrypted on-device, and rendered as a trusted
overlay that kernel-level malware on the host cannot see, forge, or tamper with.
