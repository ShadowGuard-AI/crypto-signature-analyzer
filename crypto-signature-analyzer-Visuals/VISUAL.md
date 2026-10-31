# Signature analyzer, drawn out

I made these five pictures because this tool does two different
jobs from one command line, and the split between verifying one
signature and scanning a whole collection is easier to see than
to read. Everything in them comes from the one file that does
the work, main.py, and from the README beside it.

## The visuals

- [architecture.svg](architecture.svg): the system shape. One
  argument bar at the top, then two unlike towers: verify one
  signature on the left, scan a collection on the right. They
  share the argument parsing and nothing else.
- [data-flow.svg](data-flow.svg): the scan path up close. One hex
  signature is decoded to bytes, its r value comes out of a DER
  parse or, failing that, out of splitting the raw bytes in
  half, and the r joins a set where a repeat is the finding.
- [flowchart.svg](flowchart.svg): main() as the decisions it
  actually makes, in order: load a list if one was given, verify
  if all three of key, message and signature are present, scan
  the list if there is one, and say so at exit 1 if there is
  nothing to do.
- [product-map.svg](product-map.svg): the product as six cards:
  who it serves, its two jobs, its inputs, its outputs, and
  what it does not do.

- [statistics.svg](statistics.svg): the numbers from the table below, drawn as bars on paper: lines by kind, with the file and commit counts noted under them.

Each sheet also ships as a PNG beside its SVG (architecture.png,
flowchart.png, and so on): the same drawing, finished as a
photograph of the paper page.

Every sheet here is drawn to look like what it is: a page sketched
by hand on paper, and each one is signed GK somewhere in the
drawing, never twice in the same place, because that is what I do
with a page I made.

## Written product map

What it is: a command line tool with two jobs. It verifies one
ECDSA or EdDSA signature against a PEM public key, and it scans
a collection of ECDSA signatures for nonce reuse, the same r
value appearing in more than one signature, a known key
compromise risk.

Who it serves: me, checking a signature or a batch of signatures
before trusting a key, and anyone doing the same check.

Capabilities, grouped:

- Verify one: verify_signature loads a PEM public key and checks
  a hex signature over a message. ECDSA assumes DER encoded
  signatures and SHA-256. EdDSA works with Ed25519 keys. The
  console prints VALID or INVALID, and a finished verification
  exits 0 either way.
- Scan many: analyze_ecdsa_signatures walks a list of signature
  objects, pulls the r value out of each one with extract_r,
  and reports nonce_reuse, the total signature_count, and the
  parsed_count in JSON. Unparseable entries are skipped and do
  not count as parsed.
- Reading both encodings: extract_r tries a DER parse with
  decode_dss_signature first, then falls back to raw r||s form,
  splitting the bytes in half and reading the first half as r.
- Input shapes that fail honestly: a missing file, malformed
  JSON, or a JSON value that is not a list of signatures all
  exit 1, from a file or from the -s string alike.

Inputs: for verification, a PEM public key file, a message, and
a hex signature. For a scan, a JSON file or a JSON string
holding a list of objects with a signature field. Outputs: the
VALID or INVALID line, or the JSON scan result, on the console.

Boundaries, what it does NOT do: collection analysis is ECDSA
only; EdDSA with a list gets a printed note and exit 1. The scan
detects exact r reuse only. It does not check for biased nonces,
weak randomness short of reuse, or malleability. ECDSA
verification always uses SHA-256; no other hash is offered.

## Statistics, with citations

Counted 2026-10-30, from the clone this folder ships in, at the
commit before this visuals commit (62139507022dd15ffe408ce2b95da23ebb8727d8).

| Number | Value | How it was counted, and from what |
|---|---|---|
| Files in the repo | 6 | git ls-files in the clone, counted 2026-10-30 |
| Python lines | 226 | Line count over the 1 tracked .py file (main.py), by wc -l, binary and image files excluded, 2026-10-30 |
| Markdown lines | 76 | Line count over the 1 tracked .md file (README.md), same method, 2026-10-30 |
| License lines | 21 | Line count over the tracked LICENSE file, same method, 2026-10-30 |
| requirements.txt content | 1 line of text, cryptography>=41.0.0, no trailing newline | Read directly from the tracked file, 2026-10-30; wc -l reports 0 because the file ends without a newline, so I record the content instead of a line count |
| Commits | 8 | git log in the clone, counted 2026-10-30 |
| First commit | 2025-06-17 | git log, oldest entry, 2026-10-30 |
| Last commit before this one | 2026-10-30 | git log, newest entry, 2026-10-30 |
| Tests | No test suite in the repo | No test files exist among the tracked files (git ls-files, 2026-10-30), so no tests were run and no pass is claimed |
| Run state in this campaign | Not run | The tool needs the cryptography package, which is not installed in the machine this was counted on, and I did not install anything for a documentation pass; a hand run on 2026-10-30 stopped at the import. Recorded plainly instead of claimed |
| Languages (API) | Python 8,433 bytes | GitHub API, GET /repos/ShadowGuard-AI/crypto-signature-analyzer/languages, read 2026-10-30 |
| Repo size (API) | 80 KB | GitHub API, GET /repos/ShadowGuard-AI/crypto-signature-analyzer, size field, read 2026-10-30 |
| Stars / forks (API) | 1 / 0 | Same API response, read 2026-10-30 |

External statistics: none. The r reuse risk is described in the
README in plain words, and no outside number is needed to dress
it up, so the repo's own numbers are the whole table.

## Roadmap summary

Done: both jobs, the DER and raw r extraction, and exit codes
that separate bad input from a finished answer. Working now:
nothing in flight. Next, if I return to it: a test suite, since
the repo ships none, and a run in an environment with the
cryptography package installed so the behavior is pinned by
tests instead of by reading. The full roadmap, with the Later
and Not planned sections, is in [ROADMAP.md](ROADMAP.md).
