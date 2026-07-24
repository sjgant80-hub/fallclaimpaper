# FallClaimPaper

**Live:** [sjgant80-hub.github.io/fallclaimpaper](https://sjgant80-hub.github.io/fallclaimpaper/)

**Sovereign UK claims document generator.** Single HTML file. Client data never leaves the device.
Part of the `fallclaim` bundle (anchor + onboard + paper + practice).

[![sovereign](https://img.shields.io/badge/sovereign-single--file-8b1a1a)](#) [![prime](https://img.shields.io/badge/prime-823-b8974a)](#) [![MIT](https://img.shields.io/badge/license-MIT-4ade80)](#)

---

## For the firm (1-10 person UK claims practice · CMC or solicitor)

You are an FCA-regulated claims management company **or** an SRA-regulated solicitor handling PI / RTA / EL / PL / clinical-neg / housing-disrepair / financial mis-selling / data-breach / trip claims, and you need correct paperwork in seconds — not hours.

FallClaimPaper gives you a catalogue of 14 UK-claims-shaped templates that match how cases actually run under LASPO, the Pre-Action Protocols, CPR Parts 32/35/36 and the FCA CMR rulebook:

| Template | Regime | Use case |
|---|---|---|
| **CFA Client Agreement** | LASPO Conditional Fee Agreement | Engagement on no-win-no-fee basis · success fee capped 25% PI damages |
| **DBA Client Agreement** | LASPO Damages Based Agreement | Contingency fee · capped 50% non-court / 25% PI in court |
| **Letter of Claim** | PAP Personal Injury / Clinical Neg | Pre-action protocol formal letter · parties, allegations, response deadlines |
| **Pre-Action Disclosure Request** | CPR 31.16 | Force early disclosure of key documents before issue |
| **Part 36 Offer · Claimant** | CPR Part 36 | Formal settlement offer with cost consequences |
| **Part 36 Offer · Defendant** | CPR Part 36 | Mirror offer from defendant side |
| **Schedule of Loss** | CPR 16 PD | Itemised special damages with Ogden multipliers for future loss |
| **Witness Statement** | CPR 32 PD | 1st person · paragraph numbered · statement of truth |
| **Instructions to Expert** | CPR 35 / PD 35 | Scope · SJE flag · expert duty to court |
| **Complaints Procedure Notice** | CMR / SRA | FOS or Legal Ombudsman route · time limits |
| **Cooling-off Confirmation** | CMR 14-day | Confirms client retains 14-day cancellation right |
| **Cooling-off Waiver** | CMR 14-day waiver | Explicit waiver when work must start immediately |
| **Disengagement Letter** | SRA / CMR | When firm withdraws · file handover |
| **Bill of Costs** | CPR 47 / DCMM 5 | Inter-partes assessment narrative |
| **Particulars of Claim (N1 narrative)** | CPR 16 | Pleading narrative — court still files the N1 itself |

**Generation flow:** select client (from the shared `clients` store), select case (`cases` store), select template, optionally pull live quantum from FallClaim, preview, edit unlocked sections, commit. Document is stored with sha-256 and broadcast on the `fall-claim` mesh so FallClaim / FallClaimPractice see it.

**Regulatory clauses are locked.** The CFA cap statement, DBA cap statement, FOS escalation address, FSCS limits, and statement-of-truth wording cannot be edited — they are the parts of the document that get the firm sanctioned if wrong, so the engine refuses to let you change them. Everything else is yours.

You can also export Markdown / standalone HTML and hand off to FallPDF via postMessage for archived PDF.

## For the developer

```
fallclaimpaper/
├── index.html # the deliverable · one file · IDB + BroadcastChannel
├── README.md
├── LICENSE # MIT
└── .nojekyll # GitHub Pages serves raw (no Jekyll)
```

- Vanilla JS · no build · no framework · no CDN dependencies in the core shell
- IDB stores: `firms, advisers, clients, cases, documents, templates, audit, state`
- Bundle mesh: `BroadcastChannel('fall-claim')` (clients · advisers · firm · cases · documents) + `BroadcastChannel('fall-signal')` (hello/ping)
- FallClaim valuation handoff: `postMessage({type:'fallclaim.valuation.request', caseId})`
- T0: 14 canned domain rules (CFA cap · DBA cap · Part 36 cost consequences · CPR 32 · CPR 35 · etc.) · T3: BYOK Anthropic
- Audit chain: P3 · sha-256 chained · 6-year FCA CMR retention · 7-year SRA retention
- KONOMI shim · sovereign tier · inert
- PWA manifest via `data:` URL
- Aesthetic: oxblood / brass / cream / void (Libre Baskerville / Inter / IBM Plex Mono)

### Window-exposed for verification

```js
window.FALLCLAIMPAPER = {
 state, render, audit, renderTemplate,
 TEMPLATES_BUILTIN, T0_RULES, version, prime
}
```

### Bundle siblings

| Tool | Prime | Role |
|---|---|---|
| `fallclaim` | 811 | Anchor · case intake · liability · valuation (JC Guidelines + Ogden) |
| `fallclaimonboard` | 821 | KYC / CDD / conflict / cooling-off register |
| **`fallclaimpaper`** | **823** | **Regulated document generator (this tool)** |
| `fallclaimpractice` | 827 | Fee ledger · escrow · CFA success-fee tracker · FCA CMR / SRA reporting |

### Boot-time sync

On open, FallClaimPaper emits `{type:'sync.request', source:'fallclaimpaper'}` on `fall-claim`. Any sibling that holds populated stores responds with `{type:'sync.snapshot', payload:{clients, advisers, firm, cases}}`. Receiver merges by `updatedAt` (later wins).

When FallClaimPaper saves a document it broadcasts `{type:'document.created', payload:{...}}` so FallClaim attaches the doc to the case timeline and FallClaimPractice attaches to the fee record.

### Demo seed

If IDB is empty at boot, FallClaimPaper seeds:
- one Firm record (`DEMO Claims Ltd · overwrite me`)
- one Adviser (caseworker role)
- one Client (`Alice Patel`)
- one Case (`C-2026-0001` · RTA · with valuation, liability split, defendants)
- one generated demo CFA document marked `DEMO · Alice Patel · RTA · overwrite me`

This is the Marcus Osei rule — every empty-state experience is a complete worked example you can delete.

## License

MIT. Sovereign. Fork it. Brand it. Ship it.
