**Calamari Protocol (CP)**  
*White Paper – v0.1 · April 2025*

---

### 1 · Abstract  
Calamari Protocol is an open scheduling standard for an era when software agents—not humans—set meeting times. Two AIs exchange a small set of JSON‑RPC messages, tentatively reserve a slot with a cryptographic token, then commit it to the calendars they guard. By eliminating the public “pick‑a‑time” page, CP turns scheduling into invisible machine‑to‑machine coordination and shifts the success metric from *meetings booked* to *hours preserved*.

---

### 2 · The Coordination Problem  
1. **UI drag.** Manually selecting slots locks people into a 2010 workflow while their personal AI can already draft memos.  
2. **SaaS silos.** Each vendor speaks its own dialect; agents cannot interoperate.  
3. **Seat‑based pricing.** One agent can service thousands of users, but legacy billing discourages it.  
4. **Wrong KPI.** Optimizing for more meetings feeds calendar bloat—antithetical to focus‑oriented work cultures.

The future—call it *micro‑singularity scheduling*—demands autonomous, privacy‑respecting agents that negotiate on our behalf.

---

### 3 · Design Tenets  
| Principle | Rationale |
|---|---|
| **Agent‑first** | Human eyes are optional; AIs handle discovery, hold, commit. |
| **Interoperable** | Works across Google, Microsoft, Apple and self‑hosted stacks via a shared JSON‑RPC vocabulary. |
| **Privacy‑safe** | Only salted, hashed busy blocks leave the calendar boundary. |
| **Extensible** | Optional JSON‑LD fields add context without fragmenting the core. |
| **Fail‑open** | If the guest lacks CP, the host can degrade to any legacy flow. |
| **Time‑centric KPI** | Metrics favor *hours protected* and *meeting quality* over raw booking count. |

---

### 4 · Protocol Overview  
**Transport.** HTTPS POST with JSON‑RPC 2.0; live updates through Server‑Sent Events. The wire format reuses patterns from Google’s public Agent‑to‑Agent (A2A) framework.

**Canonical flow**
```text
DISCOVER → PROPOSE → HOLD → COMMIT → SYNC
```
1. **DISCOVER** Locate the guest agent via `/.well-known/calamari.json` or DNS SRV.  
2. **PROPOSE** Host agent sends 1‑N ranked “golden slots.”  
3. **HOLD** Guest accepts one slot and receives a signed `HoldToken` (TTL 3‑15 min).  
4. **COMMIT** Both sides confirm; a standard `VEVENT` is written to each calendar.  
5. **SYNC** Optional post‑meeting transcript, recap, and nudges.

If *DISCOVER* fails, the host may revert to e‑mail, a booking page, or any API the guest understands.

---

### 5 · Core Calls & Objects  
| Call | Purpose | Key fields |
|---|---|---|
| `agent.card` | Advertise capabilities & auth endpoints | name, version, scopes |
| `availability.query` | Share hashed busy blocks & soft constraints | hashes[], preferences |
| `slot.propose` | Offer candidate slots | start, end, location, score |
| `slot.hold` | Tentatively lock a slot | HoldToken, expires_at |
| `slot.commit` | Finalize slot + transport details | VEVENT, join_link, agenda |
| `slot.decline` | Reject or counter | code, note |

*Busy‑hash recipe:* `SHA‑256(calendar‑id ‖ epoch‑start)`

---

### 6 · Security & Privacy  
| Threat | Mitigation |
|---|---|
| **Interception** | TLS‑only transport; mTLS available for regulated environments. |
| **Replay** | ES256‑signed `HoldToken`s; single‑use, nonce‑checked. |
| **Data leakage** | Only hashed busy blocks and durations transit; titles stay local. |
| **Tenant bleed** | Row‑Level Security in Postgres; per‑org KMS keys. |
| **Compliance** | SOC 2 baseline, EU/JPN data pods, HIPAA add‑on. |

---

### 7 · Reference Architecture  
```text
Gmail Add‑in   Slack /meet
     │             │
 JS SDK  →  CP Gateway (Rust or Fastify)
                ├─ Google Calendar bridge
                └─ Microsoft Graph bridge
                      │
               Postgres (holds, logs)
```
Edge cache (e.g., Cloudflare KV) stores hashed busy data for sub‑second hold negotiations.

---

### 8 · Interoperability & Stewardship  
* **Open spec (MIT).** Drafts in a public repository; issues resolved via working‑group consensus.  
* **Conformance CLI.** Agents earn a *CP‑Certified* badge only if they pass automated test suites.  
* **Versioning policy.** Semantic tagging; 6‑month deprecation notice for breaking changes.  
* **Governance board.** Calendar vendors, OSS maintainers, and enterprise adopters each hold equal votes—preventing capture by any single actor.

---

### Conclusion  
Calamari Protocol assumes a world where autonomous assistants continuously broker one another’s availability. By replacing the user‑facing grid with a minimal agent‑to‑agent contract, CP frees humans from transactional clicks and advances the industry toward a scheduling singularity—where calendars coordinate themselves and time is returned to its rightful owner.

