---
title: "Designing Asynchronous Multiparty Protocols with Crash-Stop Failures"
authors: ["Adam D. Barwell", "Ping Hou", "Nobuko Yoshida", "Fangyi Zhou"]
date: "2023-07-21"
doi: "10.4230/LIPIcs.ECOOP.2023.30"
arxiv: "2305.06238"
---

Session types provide a typing discipline for message-passing systems. However,
most session type approaches assume an ideal world: one in which everything is
reliable and without failures. Yet this is in stark contrast with distributed
systems in the real world. To address this limitation, we introduce Teatrino, a
code generation toolchain that utilises asynchronous multiparty session types
(MPST) with crash-stop semantics to support failure handling protocols.

We augment asynchronous MPST and processes with crash handling branches. Our
approach requires no user-level syntax extensions for global types and features
a formalisation of global semantics, which captures complex behaviours induced
by crashed/crash handling processes. The sound and complete correspondence
between global and local type semantics guarantees deadlockfreedom, protocol
conformance, and liveness of typed processes in the presence of crashes.

Our theory is implemented in the toolchain Teatrino, which provides correctness
by construction.  Teatrino extends the Scribble multiparty protocol language to
generate protocol-conforming Scala code, using the Effpi concurrent programming
library. We extend both Scribble and Effpi to support crash-stop behaviour. We
demonstrate the feasibility of our methodology and evaluate Teatrino with
examples extended from both session type and distributed systems literature.
