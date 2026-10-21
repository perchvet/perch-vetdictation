# Perch blind dictation corpora

420 written veterinary dictations and traps in six blind corpora, each read aloud by three Kokoro voices. 1,260 clips.

## What this is

These are six blind test corpora for one question: when a veterinarian dictates, does a speech system or scribe write down the drugs that were said, and only those? Each corpus has 70 rows.

- 45 are ordinary veterinary dictations, 21 to 77 spoken words each, written in the voice of the person who would dictate them: SOAP visits, discharge talks, phone calls, referral letters, rounds. Across the six corpora the 270 dictations name 547 drugs from a fixed formulary of 66 brand and generic names.
- 25 are traps, built on purpose to bait a system that guesses drug names from sound into offering a drug nobody said. Some use ordinary words that sound like a brand ("front line," "entice," "serene"). Some say real veterinary drugs that are not on the formulary (Proin, Dasuquin, selegiline) and mean them as themselves. Some use the names of pets, clients, staff or products that sound drug adjacent (a dog called Bravo, a client called Mrs. Sereno, a technician called Clinda). A few are clinical content with no drug at all, such as a lab readout.

Nineteen of the 150 traps also say an on list drug. Those rows list it in `drugs`, and it is scored like any dictated drug.

Each row was read aloud by three Kokoro voices. Each corpus has 210 clips and the six together have 1,260, about 308 minutes of audio.

The rows were written by authors who never saw the matching code and were told nothing about how any scribe works. We built them to test our own product, [Perch](https://perch.talcoe.com), and we are publishing all of it: the rows, the audio and the scoring rule.

## The six corpora

Each corpus has its own author and its own register, the setting its dictations come from. Ids start with one letter per corpus and run 001 to 070. Dictations are 001 to 045 and traps are 046 to 070.

| Corpus | Ids | Register | Voices |
|---|---|---|---|
| blind1 | f001 to f070 | SOAP visits with casual dispensing language, across general small animal practice | af_bella, am_adam, bm_lewis |
| blind2 | h001 to h070 | Discharge talks, referral letters, technician treatment sheets, feline cases, hospice, plus phone triage, anesthesia records, second opinions and case history recounts | af_bella, am_adam, bm_lewis |
| blind3 | j001 to j070 | Emergency and overnight ER, shelter intake and wards, specialty referral letters, poison control, boarding sheets, phone triage, hospice, refill callbacks | af_bella, am_adam, bm_lewis |
| blind4 | k001 to k070 | Dental charting, anesthesia and surgery sheets, oncology chemo protocols, nutrition and behavior consults, laboratory callbacks, client education handouts read aloud, very fast list dictation, a few rows in non native English phrasing | af_sarah, am_liam, bf_isabella |
| blind5 | m001 to m070 | Euthanasia paperwork, ophthalmology and dermatology rechecks, feline chronic disease management (renal, thyroid, diabetic), puppy and kitten series visits, radiology and ultrasound reports with a medication plan, internal medicine workups, insurance claim narratives read aloud, voicemail summaries | af_nova, am_puck, bf_alice |
| blind6 | r001 to r070 | Rounds and ICU handover, post op rechecks, senior wellness reconciliation, cardiology titration, urgent care, house calls, telemedicine, health certificates, pharmacy callbacks | af_aoede, am_echo, bm_fable |

| Corpus | Dictations | Traps | Drug mentions in dictations | Distinct names | Clips | Minutes of audio |
|---|---|---|---|---|---|---|
| blind1 | 45 | 25 | 81 | 60 | 210 | 60 |
| blind2 | 45 | 25 | 80 | 52 | 210 | 67 |
| blind3 | 45 | 25 | 108 | 52 | 210 | 40 |
| blind4 | 45 | 25 | 85 | 46 | 210 | 38 |
| blind5 | 45 | 25 | 100 | 51 | 210 | 54 |
| blind6 | 45 | 25 | 93 | 49 | 210 | 49 |
| Total | 270 | 150 | 547 | 65 | 1,260 | 308 |

A drug mention is one entry in `drugs`; a drug named twice in one row is listed once. Distinct names counts the different names listed anywhere in a corpus's 70 rows, trap rows included. Every name listed is one of the 66 formulary names, and 65 of the 66 appear in at least one row. The 19 trap rows that also say an on list drug add 23 more mentions.

Voice ids follow Kokoro's naming. The first letter is the accent (a American, b British) and the second is the gender (f female, m male). Each corpus has one American female voice, one American male voice and one British voice. blind1, blind2 and blind3 share the same three voices. blind4, blind5 and blind6 each use a different set. In the file names the voice carries the engine, for example `kokoro_af_bella`.

## How the rows were written

- Each corpus has its own author. The author got a clinical brief, the formulary and a register, and nothing else.
- The author was told not to open the matching code, any note on how it works, any earlier corpus or the project history. The only file the author could read was a structure checker, to understand a failure message.
- Drug names are spelled exactly as the formulary spells them, in `ref` and in `spoken`.
- The brief asked the author to vary the dictations heavily. Some give a full sig beside the drug. Some name drugs with no dose at all, as a history list does ("failed Apoquel"). Some say brand and generic together ("that's pimobendan"). Some rattle off three or four drugs in one breath. Some put the drug far from its dose. Some use the drug as a plain noun ("out of Convenia"). Each setting uses its own phrasing for medication: "bolused," "drew up," "came in on," "transferred on," "dewormed on intake," "currently maintained on," "was trialled on," "please continue," "no changes to."
- The brief asked for 25 traps per corpus in four shapes, in roughly these numbers: about 10 ordinary words that sound like formulary brands, used in their ordinary sense; about 8 real veterinary drugs that are not on the formulary, alone or beside on list ones; about 4 names of pets, clients, staff, kennels or products that sound drug adjacent; about 3 clinical rows with no drug at all.
- A structure checker had to pass before any audio was rendered. It requires 70 rows, 45 dictations and 25 traps; no digits and no shorthand in `spoken`; every drug in `drugs` spelled as the formulary spells it and present in both `ref` and `spoken`; every formulary name in `ref` listed, unless it is an ordinary lowercase word; and a reason written on every trap.
- The brief for corpus 3 was saved, so later authors start from the same rules and only the register changes. The first two corpora were written from prompts that were not saved.

The audio is `spoken` rendered by Kokoro v1.0, three voices per corpus, at 16 kHz mono. The clips here are FLAC. The step from the rendered WAV to FLAC is lossless: the decoded samples of all 1,260 files match the WAVs.

## Files

```
data/blind1.jsonl to data/blind6.jsonl   the 70 rows of each corpus
data/manifest.jsonl                      one row per clip, 1,260 rows
audio/blindN/<voice>/<id>.flac           the clips (not in git: release assets and Hugging Face)
scoring/README.md                        the scoring rule and a reference scorer
```

`data/blindN.jsonl` has one JSON object per line:

| Field | Meaning |
|---|---|
| `corpus` | `blind1` to `blind6` |
| `id` | the row id as authored |
| `kind` | `dictation` or `trap` |
| `category` | a short tag for the setting, chosen by the author |
| `ref` | the written form a clinician would file: digits and standard shorthand (mg, PO, BID) |
| `spoken` | exactly what the voice read: no digits, no shorthand ("sixteen milligrams by mouth twice daily") |
| `drugs` | every formulary name in `ref`, spelled as the formulary spells it, once each; empty when none |
| `trap` | empty on dictations; on traps, one sentence saying what the row is built to fool |

The rows are the authored rows, unchanged. The only field added is `corpus`. A trap row:

```json
{"corpus": "blind3", "id": "j066", "kind": "trap", "category": "boarding_sheet", "ref": "Boarding drop off went smoothly, staff notes the dog is reactive to other dogs and should stay in the Rex Guard Kennels wing away from the play yard.", "spoken": "Boarding drop off went smoothly, staff notes the dog is reactive to other dogs and should stay in the Rex Guard Kennels wing away from the play yard.", "drugs": [], "trap": "Rex Guard Kennels is the name of the boarding wing, not a heartworm product like Heartgard or NexGard."}
```

`data/manifest.jsonl` has one row per clip, with the fields `corpus`, `id`, `voice`, `file`, `duration_sec`, `drugs`, `trap`, `ref` and `spoken`. `file` is the path of the FLAC relative to the repository root. `duration_sec` is the clip length in seconds. The same dictation appears three times, once per voice. A row is a trap when `trap` is not empty. To get `kind` and `category`, join to `data/blindN.jsonl` on `corpus` and `id`. The first manifest row:

```json
{"corpus": "blind1", "id": "f001", "voice": "kokoro_af_bella", "file": "audio/blind1/kokoro_af_bella/f001.flac", "duration_sec": 15.296, "drugs": ["Apoquel", "oclacitinib"], "trap": "", "ref": "8-year-old male neutered Labrador, 32 kg, presents for chronic pruritus. Starting Apoquel, oclacitinib, 16 mg PO BID for 14 days, then taper to SID. Recheck in 2 weeks.", "spoken": "Eight year old male neutered Labrador, thirty two kilograms, presents for chronic pruritus. Starting Apoquel, oclacitinib, sixteen milligrams by mouth twice daily for fourteen days, then taper to once daily. Recheck in two weeks."}
```

## Getting the audio

The audio is not in the git tree. It ships two ways:

1. **GitHub release assets.** One zip per corpus, `blind1-audio.zip` to `blind6-audio.zip` (about 336 MB together), and a `SHA256SUMS` file. https://github.com/perchvet/perch-vetdictation/releases
2. **Hugging Face.** https://huggingface.co/datasets/Talcoe/perch-vetdictation holds the same FLAC files at the same paths, next to the data files.

For the release assets, put the zips and `SHA256SUMS` in one folder and run `sha256sum -c SHA256SUMS` (on macOS, `shasum -a 256 -c SHA256SUMS`). Then unzip each zip in the repository root. The paths inside the zips are `audio/blindN/<voice>/<id>.flac`, the same as the `file` column of `data/manifest.jsonl`.

Every clip is FLAC, 16 kHz, mono, 16 bit.

## How to run it on your own system

1. Get the audio (above).
2. Run your system on every clip in `data/manifest.jsonl`. Each of the 1,260 clips is its own case.
3. Keep two things per clip: the transcript, if your system exposes one, and the list of drug names it offered (charted, suggested or shown to the clinician). Write one line of JSON per clip:

   ```json
   {"corpus": "blind1", "id": "f001", "voice": "kokoro_af_bella", "transcript": "<text, or an empty string>", "offered": ["Apoquel", "oclacitinib"]}
   ```

4. Score it with the rule below. `scoring/README.md` has the full rule and a short reference scorer.

To read a clip in Python (needs `pip install soundfile`):

```python
import json
import soundfile as sf

rows = [json.loads(line) for line in open("data/manifest.jsonl", encoding="utf-8")]
audio, rate = sf.read(rows[0]["file"])   # 16000 Hz, mono
```

## How scoring works

- **Recovered.** A drug in a row's `drugs` counts as recovered on a clip when its name appears, correctly spelled, in the transcript or in the offered list. Names are compared after lowercasing and dropping everything but letters and digits. One spelling variant is credited: cephalexin and cefalexin.
- **Missed.** A drug in `drugs` that is not recovered.
- **False offer.** A drug name your system offers on a clip that is not in the row's `drugs`. On a trap row that lists no drug, every drug offered is a false offer. Two things are never false offers: the brand or generic partner of a drug in `drugs`, and a real drug that is not on the formulary and that the row genuinely says (Proin on the Proin row).
- Each row is read by three voices. Count each clip, so a drug listed once in a row is three dictated drug instances in its corpus.
- **Report misses and false offers per corpus, as raw counts.** This repository gives no rate.

The exact rule, the sanity checks and a reference scorer are in [scoring/README.md](scoring/README.md). The companion test is the trap set, [perch-trapset](https://github.com/perchvet/perch-trapset), which counts the same things on a different set of clips.

## What this is not

- **Not recordings of real visits.** The rows are written dictations and the audio is synthetic speech from three Kokoro voices, not a clinician in an exam room. Treat any result as a reproducible shared test, not an absolute accuracy claim.
- **Not a set of results.** This repository holds the corpora and the rule. It reports no result for any product, and no rate.
- **Blind describes how the rows were written.** The authors never saw the matching code. That does not keep the rows unseen for every system: a system whose builders have read these rows can be tuned to them.

## License

The rows (`data/`), the audio and the other text in this repository are licensed under **CC BY 4.0**. Code, including the reference scorer in `scoring/README.md`, is licensed under the **Apache License 2.0**. Full text of both is in `LICENSE`.

The voices are Kokoro (v1.0, by hexgrad), released under the Apache License 2.0: https://huggingface.co/hexgrad/Kokoro-82M. The audio here is the output of that model, and the credit for the voices goes to its authors.

## Citation

See `CITATION.cff`. In short: Talcoe LLC, "Perch blind dictation corpora: 420 veterinary dictations and traps with Kokoro audio," version 1.0.0, 2026. DOI pending.

The same files are also on Hugging Face: https://huggingface.co/datasets/Talcoe/perch-vetdictation

## Related

- [perch-trapset](https://github.com/perchvet/perch-trapset) ([Hugging Face](https://huggingface.co/datasets/Talcoe/perch-trapset)): the companion test. Blind clips built to catch a scribe that charts a drug or a dose nobody said.
- [vet-drug-pronunciations](https://github.com/perchvet/vet-drug-pronunciations) ([Hugging Face](https://huggingface.co/datasets/Talcoe/vet-drug-pronunciations)): pronunciations for veterinary drug names.
- [perch-phoneme-lane](https://github.com/perchvet/perch-phoneme-lane): the code for Perch's phoneme lane.

---

Talcoe LLC, makers of Perch. Questions: mo@talcoe.com
