---
title: "Building the BEP 3 handshake in C++"
date: 2026-10-16
tags: ["c++", "networking"]
---

The handshake is a fixed 68-byte message. Build it once with `std::array`, then reuse the buffer for every peer.

```cpp
#include <array>
#include <cstdint>
#include <cstring>
#include <string_view>

namespace bt {

constexpr std::string_view kProtocol = "BitTorrent protocol";   // pstr
constexpr std::size_t kHandshakeLen = 49 + kProtocol.size();    // 68

using Sha1 = std::array<std::uint8_t, 20>;

// <pstrlen><pstr><reserved:8><info_hash:20><peer_id:20>
std::array<std::uint8_t, kHandshakeLen>
make_handshake(const Sha1& info_hash, const Sha1& peer_id) {
    std::array<std::uint8_t, kHandshakeLen> out{};
    out[0] = static_cast<std::uint8_t>(kProtocol.size());
    std::memcpy(out.data() + 1, kProtocol.data(), kProtocol.size());
    // 8 reserved bytes stay zero
    std::memcpy(out.data() + 28, info_hash.data(), info_hash.size());
    std::memcpy(out.data() + 48, peer_id.data(), peer_id.size());
    return out;
}

} // namespace bt
```

Put the language after the opening fence (`cpp`, `python`, `sh`, `toml`, `yaml`, `json`, `rust`, `go`, `js` …) and it is coloured as on GitHub. A long line scrolls sideways instead of breaking the page:

```cpp
auto it = std::find_if(peers_.begin(), peers_.end(), [&](const Peer& p) { return p.info_hash == hash && p.state == PeerState::Connected && !p.choked_us; });
```

Other languages, same colours:

```python
def piece_hash(data: bytes) -> bytes:
    import hashlib
    return hashlib.sha1(data).digest()   # 20 bytes
```

```sh
hugo server -D   # preview with drafts
```

Inline code like `ev_async_send(loop, &wake)` stays plain.
