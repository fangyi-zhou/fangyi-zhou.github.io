---
title: "Generating Interactive WebSocket Applications in TypeScript"
authors: ["Anson Miu", "Francisco Ferreira", "Nobuko Yoshida", "Fangyi Zhou"]
date: "2020-04-03"
doi: "10.4204/EPTCS.314.2"
arxiv: "2004.01321"
---
Advancements in mobile device computing power have made interactive web
applications possible, allowing the web browser to render contents dynamically
and support low-latency communication with the server. This comes at a cost to
the developer, who now needs to reason more about correctness of communication
patterns in their application as web applications support more complex
communication patterns.

Multiparty session types (MPST) provide a framework for verifying conformance
of implementations to their prescribed communication protocol. Existing
proposals for applying the MPST framework in application developments either
neglect the event-driven nature of web applications, or lack compatibility with
industry tools and practices, which discourages mainstream adoption by web
developers.

In this paper, we present an implementation of the MPST framework for
developing interactive web applications using familiar industry tools using
TypeScript and the React.js framework. The developer can use the Scribble
protocol language to specify the protocol and use the Scribble toolchain to
validate and obtain the local protocol for each role. The local protocol
describes the interactions of the global communication protocol observed by the
role. We encode the local protocol into TypeScript types, catering for
server-side and client-side targets separately. We show that our encoding
guarantees that only implementations which conform to the protocol can
type-check. We demonstrate the effectiveness of our approach through a
web-based implementation of the classic Noughts and Crosses game from an MPST
formalism of the game logic.
