# Signature analyzer roadmap

Written 2026-10-30 from the repo as it stands. No dates on the
future items, because I do not know when I will be back in this
code, and a date I invent here would be a small lie in a public
file.

## Done

- Verify one signature: ECDSA in DER form with SHA-256 and
  EdDSA with Ed25519 keys, against a PEM public key, printing
  VALID or INVALID and exiting 0 for either finished answer.
- Scan a collection for ECDSA nonce reuse: the same r value in
  more than one signature sets nonce_reuse in a JSON result
  that also reports how many entries were given and how many
  parsed.
- extract_r reading both encodings: a DER parse first, then
  the raw r||s fallback that splits the bytes in half.
- Bad input handled as bad input: missing files, malformed
  JSON, and JSON that is not a list of signatures exit 1,
  whether the list came from a file or from the -s string.

## Working now

- Nothing in flight. The repo holds no TODO or FIXME notes
  (I searched the tracked files on 2026-10-30) and no test
  suite.

## Next

- A test suite. The repo ships none, and the DER against raw
  split in extract_r is exactly the kind of behavior a few
  known signatures would pin down.
- A verification and scan run in an environment with the
  cryptography package installed, recorded with its output, so
  the pass state rests on a run and not on reading the code.

## Later

- Nothing honest to put here yet. The limits below name the
  nearby risks this tool does not check; whether any of them
  belongs in this tool is a decision for when I am back in it,
  not a promise made now.

## Not planned

- Collection analysis for EdDSA. Nonce reuse is an ECDSA
  failure mode; the tool says analysis is ECDSA only and that
  stays.
- Calling an r match anything stronger than what it is: a
  sign that the signing key behind those signatures should be
  treated as compromised, found by exact match only.
