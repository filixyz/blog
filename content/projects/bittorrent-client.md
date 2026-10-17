---
title: "BitTorrent client in C++"
date: 2026-10-10
tags: ["networking", "c++", "systems"]
---

A BitTorrent client written from scratch in C++, built bottom-up to show a complex
protocol architecture turned into working software.

- **Done and validated:** bencode decoder, .torrent abstraction, HTTP tracker manager
  (libcurl multi-socket inside a libev event loop, with reannounce and backoff), and a
  connection manager that completes BEP 3 handshakes with live peers.
- **In progress:** the transfer manager (peer wire messages, piece selection and
  hashing), then the file manager.
- **Design:** two-thread peer/transfer architecture with SPSC queues and `ev_async`
  handoffs, a ring-buffer transport using scatter-gather `recvmsg`/`sendmsg`, and
  rarest-first piece selection. Checked under AddressSanitizer and UBSan.

Link the repository here.
