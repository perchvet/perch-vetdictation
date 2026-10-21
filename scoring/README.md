# Scoring

How to score a system against the six blind dictation corpora. The rule counts. It does not compute a rate.

The companion test, [perch-trapset](https://github.com/perchvet/perch-trapset), counts the same three things: drugs found, drugs missed and false offers. There the list a system produced is called `charted`. Here it is called `offered`.

## What you produce

Run your system on every clip in `data/manifest.jsonl`. Write one line of JSON per clip:

```json
{"corpus": "blind1", "id": "f001", "voice": "kokoro_af_bella", "transcript": "<text, or an empty string>", "offered": ["Apoquel", "oclacitinib"]}
```

- `corpus`, `id` and `voice` identify the clip. Copy all three from the manifest row; a line whose three values match no manifest row is ignored.
- `transcript` is the text your system produced for the clip. Use an empty string if it does not expose one.
- `offered` is the list of drug names your system put forward for the clip: the medications it charted, suggested or showed the clinician. One drug name per entry, written as your system writes it, with no dose or route in the entry. Use an empty list if there are none.

A clip with no output line counts as empty output. The reference scorer reports how many clips had none.

## The rule

A dictated drug is one entry of a row's `drugs` list. Every clip of a row has the same dictated drugs. Three voices read each row, so a drug listed once in a row is three dictated drug instances in its corpus.

Names are compared after lowercasing and dropping every character that is not a letter or a digit, so `Amoxicillin-Clavulanate` and `amoxicillin clavulanate` are the same name. Cephalexin and cefalexin are treated as one drug.

**Recovered.** A dictated drug is recovered on a clip when its name appears, correctly spelled, in `transcript` or in an entry of `offered`. In `transcript` the name may sit inside a longer sentence.

**Missed.** A dictated drug that is not recovered.

**False offer.** An entry of `offered` is a false offer on a clip when all three of these hold:

1. It is not one of the row's `drugs`.
2. It is not the brand or generic partner of one of the row's `drugs`. Apoquel and oclacitinib are partners. The reference scorer below starts with an empty pair table. The trap set's `score.mjs` lists many pairs in `DRUG_PAIRS`; use that table or your own.
3. It is not an off formulary drug that the row genuinely says. A name is off formulary when it is listed in no row's `drugs` in any of the six corpora, so judge that against all six files even when you score one. A name is genuinely said when its words occur in the row's `spoken` text as whole words, in order. Proin on the Proin row passes this test.

Each distinct wrong name counts once per clip.

The third statement has that shape because some traps say a real drug the formulary does not have, and charting what was said is correct. An ordinary word gets no such cover. "Front line" does not say Frontline, and "a revolution in shelter medicine" does not say Revolution, because both are formulary names.

The rule reads `offered` for false offers. It does not scan transcripts for invented drug names. If your system exposes only a transcript, read the transcripts of the trap rows by eye.

## What to report

For each corpus, the counts: clips, clips with no output, dictated drug instances, recovered, missed and false offers.

Dictated drug instances, so you can check your own tables:

| Corpus | Clips | Dictated drug instances |
|---|---|---|
| blind1 | 210 | 252 |
| blind2 | 210 | 252 |
| blind3 | 210 | 342 |
| blind4 | 210 | 261 |
| blind5 | 210 | 315 |
| blind6 | 210 | 288 |
| Total | 1,260 | 1,710 |

Report misses and false offers per corpus as counts. This repository gives no rate. If you publish one, give the counts beside it and say that the audio is synthetic speech.

## Two checks before you trust your own numbers

An output file that offers exactly each row's `drugs` and uses `spoken` as the transcript scores zero missed and zero false offers. An empty output file scores every dictated drug as missed and no false offers. If the scorer does not give those two results on your files, fix the files before you compare anything.

## Reference scorer

Python 3, standard library only, Apache License 2.0. Save it as `score.py` and run:

```
python score.py data/manifest.jsonl outputs.jsonl
```

```python
# Reference scorer for the Perch blind dictation corpora.
# Apache License 2.0, Talcoe LLC. Python 3, standard library only.
#
# Usage: python score.py data/manifest.jsonl outputs.jsonl
#
# outputs.jsonl has one line per clip:
#   {"corpus": "blind1", "id": "f001", "voice": "kokoro_af_bella",
#    "transcript": "...", "offered": ["Apoquel", "oclacitinib"]}
import json
import re
import sys

# Spelling variants of one drug.
ALIASES = [("cephalexin", "cefalexin")]
# Brand and generic pairs, as (brand, generic). Fill in the pairs your system needs.
# The trap set's score.mjs lists many of them in DRUG_PAIRS.
PAIRS = []


def read_rows(path):
    with open(path, encoding="utf-8") as f:
        return [json.loads(line) for line in f if line.strip()]


def norm(text):
    return re.sub(r"[^a-z0-9]", "", str(text).lower())


def words(text):
    return re.findall(r"[a-z0-9]+", str(text).lower())


def pair_of(table, a, b):
    return any({norm(a), norm(b)} == {norm(x), norm(y)} for x, y in table)


def same_drug(a, b):
    return norm(a) == norm(b) or pair_of(ALIASES, a, b)


def spelled(drug, texts):
    names = [drug] + [y for x, y in ALIASES if norm(x) == norm(drug)] + [x for x, y in ALIASES if norm(y) == norm(drug)]
    return any(norm(n) and norm(n) in norm(t) for n in names for t in texts)


def genuinely_said(name, spoken, listed):
    """An off formulary drug whose words occur in the spoken text, whole words, in order."""
    if norm(name) in listed:
        return False
    w, s = words(name), words(spoken)
    return bool(w) and any(s[i:i + len(w)] == w for i in range(len(s) - len(w) + 1))


def score_clip(row, out, listed):
    texts = [str(out.get("transcript") or "")] + [str(x) for x in out.get("offered") or []]
    recovered = [d for d in row["drugs"] if spelled(d, texts)]
    missed = [d for d in row["drugs"] if d not in recovered]
    false_offers, seen = [], set()
    for name in out.get("offered") or []:
        key = norm(name)
        if not key or key in seen:
            continue
        seen.add(key)
        if any(same_drug(name, d) for d in row["drugs"]):
            continue
        if any(pair_of(PAIRS, name, d) for d in row["drugs"]):
            continue
        if genuinely_said(name, row["spoken"], listed):
            continue
        false_offers.append(name)
    return recovered, missed, false_offers


def main(manifest_path, outputs_path):
    manifest = read_rows(manifest_path)
    outputs = {(o["corpus"], o["id"], o["voice"]): o for o in read_rows(outputs_path)}
    scored = {o[0] for o in outputs}
    listed = {norm(d) for m in manifest for d in m["drugs"]}
    totals = {}
    for m in manifest:
        if m["corpus"] not in scored:
            continue
        t = totals.setdefault(m["corpus"], dict(clips=0, no_output=0, dictated=0, recovered=0, missed=0, false_offers=0))
        out = outputs.get((m["corpus"], m["id"], m["voice"]))
        t["clips"] += 1
        if out is None:
            t["no_output"] += 1
            out = {}
        recovered, missed, false_offers = score_clip(m, out, listed)
        t["dictated"] += len(m["drugs"])
        t["recovered"] += len(recovered)
        t["missed"] += len(missed)
        t["false_offers"] += len(false_offers)
    print("corpus   clips  no_output  dictated  recovered  missed  false_offers")
    for name in sorted(totals):
        t = totals[name]
        print(f"{name:<8}{t['clips']:>6}{t['no_output']:>11}{t['dictated']:>10}{t['recovered']:>11}{t['missed']:>8}{t['false_offers']:>14}")


if __name__ == "__main__":
    main(sys.argv[1], sys.argv[2])
```

It prints one line per corpus that appears in your outputs, with the columns `clips`, `no_output`, `dictated`, `recovered`, `missed` and `false_offers`. A corpus that has no line in your outputs is left out. A clip of a listed corpus that has no line counts as empty output.
