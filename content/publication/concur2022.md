---
title: "Generalised Multiparty Session Types with Crash-Stop Failures"
authors: ["Adam D. Barwell", "Alceste Scalas", "Nobuko Yoshida", "Fangyi Zhou"]
date: "2022-09-12"
doi: "10.4230/LIPIcs.CONCUR.2022.3"
arxiv: "2207.02015"
math: true
---

Session types enable the specification and verification of communicating
systems. However, their theory often assumes that processes never fail. To
address this limitation, we present a generalised multiparty session type
(MPST) theory with crash-stop failures, where processes can crash arbitrarily.

Our new theory validates more protocols and processes w.r.t. previous work. We
apply minimal syntactic changes to standard session $\pi$-calculus and types:
we model crashes and their handling semantically, with a generalised MPST
typing system parametric on a behavioural safety property. We cover the
spectrum between fully reliable and fully unreliable sessions, via optional
reliability assumptions, and prove type safety and protocol conformance in the
presence of crash-stop failures.

Introducing crash-stop failures has non-trivial consequences: writing correct
processes that handle all crash scenarios can be difficult. Yet, our
generalised MPST theory allows us to tame this complexity, via model checking,
to validate whether a multiparty session satisfies desired behavioural
properties, e.g. deadlock-freedom or liveness, even in presence of crashes. We
implement our approach using the mCRL2 model checker, and evaluate it with
examples extended from the literature.
