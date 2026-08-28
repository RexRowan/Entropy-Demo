
# Entropy Demo

**Rex sends a message to FutureRex. Entropy gets there first.**

[Live demo →](https://rexrowan.github.io/Entropy-Demo/)

A single-page, single-file demo: a text message is "teleported" forward in time to a future self, and the time in between is modeled as a noisy quantum channel. Watch the same message survive unprotected, get corrupted, or get corrected — live, as you turn the dials.

## The idea

Sending information anywhere — across a wire, across a room, or forward in time to your future self — means it has to survive whatever the channel does to it in between. Here, the channel is decoherence: the longer the message waits, the more likely each bit has flipped by the time it arrives. That's entropy, made visible instead of asserted.

The demo lets you choose how much you trusted that channel going in:

- **Unprotected** — the raw message, exposed to noise with no defense at all.
- **3-qubit repetition code** — each bit tripled, corrected by majority vote.
- **Steane `[[7,1,3]]` code** — each bit spread across 7 qubits arranged on a Fano plane, corrected via real Hamming-parity syndrome extraction: three parity checks pinpoint the exact faulty qubit (if there is exactly one), and it gets flipped back.

An optional dynamical-decoupling toggle extends the effective coherence time, illustrating the general shape of DD's benefit without simulating a specific pulse sequence.

## What it actually simulates (and what it doesn't)

Worth being precise about, rather than letting "quantum teleportation lab" in the header do more work than the model behind it:

- **The noise model is classical, not a real quantum channel.** Each physical bit flips independently with probability `1 - exp(-t/T1)` — a standard exponential decay curve, applied directly to classical bits. There's no statevector, no density matrix, no actual qubit being simulated underneath.
- **Only bit-flip (X-type) errors are modeled.** The real Steane code corrects both bit-flip and phase-flip errors using two interleaved parity-check structures. This demo implements one Hamming-code parity structure (the classical code the X-part of Steane is built from) and applies it to a single classical bit-flip channel — it does not model phase errors at all, so it's showing half of what the real code does.
- **Syndrome extraction is exact arithmetic, not simulated measurement.** The three parity checks (`s1`, `s2`, `s3`) are computed directly from the corrupted bit array. A real implementation would extract that syndrome via ancilla qubits and mid-circuit measurement, which itself has a failure rate this demo doesn't account for.
- **Dynamical decoupling is a flat multiplier**, not a simulated pulse sequence — turning it on scales the effective `T1` by a fixed 1.8×, standing in for the general idea of DD extending coherence rather than modeling XY4 or any specific sequence.
- **"IBM Heron Architecture" in the header is flavor text.** It sets the scene; no Heron-specific noise parameters, connectivity, or gate fidelities are actually used anywhere in the simulation.

None of this is hidden in the code — it's straightforward arithmetic in a single `index.html` — but it's worth saying plainly here rather than letting the polished UI imply more rigor than a from-scratch classical approximation actually has.

## Running it

No build step, no dependencies to install:

```bash
git clone https://github.com/RexRowan/Entropy-Demo
cd Entropy-Demo
open index.html   # or just double-click it
```

React, ReactDOM, and Babel are all loaded from CDN at runtime; Tailwind is loaded the same way. It's one file.

## Roadmap

- **Model phase-flip errors too.** Add the second (Z-type) parity structure so the Steane code actually corrects what it claims to, not just its classical Hamming half.
- **A real circuit-simulation mode.** Offer an alternate mode that builds the actual encoding, syndrome-extraction, and correction circuit in Qiskit and runs it on `qiskit-aer`, so the closed-form classical approximation here can be cross-checked against a real quantum simulation rather than trusted on its own.
- **Model syndrome-extraction failure.** Ancilla measurement isn't free or perfect — give syndrome extraction itself a failure probability instead of treating it as exact arithmetic.
- **Replace the flat DD multiplier** with an effective decoherence curve derived from an actual simulated pulse sequence (starting with XY4, since that's what the UI already claims is active).
- **A concatenated or small surface-code option**, to show the qubit-overhead/protection tradeoff at a scale beyond a single `[[7,1,3]]` block.
- **Multi-hop relay mode** — chain several teleportation "hops" in sequence, so accumulated failure probability over a relay becomes visible instead of only a single wait time.
- **Shareable permalinks** that encode the message and settings in the URL, so a specific run can be sent to someone else instead of only described.
