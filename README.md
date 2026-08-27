# ezv_for_diffsinger v2.0

**English** | [한국어](README.ko.md)

A **pitch-controllable neural vocoder** for OpenUtau + DiffSinger voicebanks.
44.1 kHz / hop 512 / 128-bin mel — drop-in compatible with the standard
DiffSinger vocoder interface.

> Stable release (architecture codename: **pc-ansf-next**).
> The vocoder of the **EZSing** project.
> Freeware — **rendered audio is royalty-free, commercial use OK.**
> The vocoder itself: no selling/bundling, no redistribution, no
> modification. See `LICENSE.txt`.

## What it does

- **True pitch control (PC)**: harmonics are generated directly from the f0
  curve by an additive source, so pitch stays accurate even far outside the
  voicebank's recorded range (verified up to +18 semitones with stable
  timbre). Combined with the
  [diffsinger-pitch-limit](https://github.com/matax2bi/diffsinger-pitch-limit)
  tool, any voicebank can sing high notes with a healthy timbre.
- **CPU realtime**: renders faster than realtime on CPU (RTF ≈ 0.7) — no
  GPU required. `force_on_cpu` is set on purpose: the graph is a chain of
  many small ops, which measures consistently faster on CPU than on
  GPU/DirectML.

## 🎧 Demo

- `v2.0_demo.mp4` — render demo (**with mixing**)
- `v2.0_demo2.mp4` — same song, **raw vocoder output** (unprocessed)

## New in v2.0 (vs v1.0)

- **Native DS-grid pipeline**: the whole model now runs natively on the
  acoustic model's frame grid (44.1 kHz / hop 512). The internal
  resampling layer of v1.x — and every artifact it caused — is gone,
  and rendering got roughly 2× lighter.
- **Short-note noise fixed**: the boundary energy post-processing
  (snap/fade bridges) of v1.x has been retired; noise on short notes
  before aspirated consonants (k/t/p/ch/ts) is gone.
- **Learned silence**: the vocoder chain now learns to render imperfect
  silence from acoustic models as true silence — no more hiss in the
  closure gaps between a vowel and the following consonant.
- **Noise-floor supervision**: the noise level between harmonics is now
  trained directly against reference — breathiness sits closer to the
  real voice.
- **High-note stability**: redesigned phase engine — no micro-detune on
  long renders or sustained high notes.
- **Groundwork for articulation transients**: a new release-transient
  path (tongue/lip release of n/m-type consonants) is built into this
  version and will mature in updates.
- **30% smaller**: package 157 MB → 127 MB.

## Install (OpenUtau)

**Easiest — .oudep drag & drop:**
Drag `ezv_for_diffsinger.oudep` onto the OpenUtau window (or use
`Tools → Install Dependency`). Done — skip to step 2 below.

**Manual (zip):**
1. Copy the `ezv_for_diffsinger` folder into OpenUtau's
   `Dependencies` folder:
   `OpenUtau/Dependencies/ezv_for_diffsinger/`
   (the folder must contain `ezv_for_diffsinger.onnx` and `vocoder.yaml`)
2. In your DiffSinger voicebank's `dsconfig.yaml`, set:
   ```yaml
   vocoder: ezv_for_diffsinger
   ```
3. Render. If upgrading from v1.x/beta, clear OpenUtau's render cache
   (`Tools → Clear Cache`) so old renders are not reused.

## Usage (voicebank setup)

To unlock the vocoder's voicing control and extended-range features,
patch your voicebank's acoustic model once, using the
[diffsinger-pitch-limit](https://github.com/matax2bi/diffsinger-pitch-limit)
tool:

1. **Edit dsdict** — fix the `symbols:` types in the
   voicebank's dsdict.yaml to match each phoneme's nature:
   - voiceless consonants → `stop` (fricative/affricate/aspirate also
     recognized)
   - always-voiced consonants (nasals, liquids, ...) → `nasal`,
     `liquid`, `voiced`
   - consonants whose voicing depends on position (e.g. Korean lenis
     g/d/b/j) → `lenis` (the vocoder decides per frame)
2. **Patch the acoustic** — drag & drop the acoustic onnx onto the
   pitch-limit tool's bat file → set the range limits (high/low), and
   answer `y` to the uv-mark question.
3. **Edit dsconfig** — confirm the patched onnx was created, then point
   the voicebank's `dsconfig.yaml` to it:
   ```yaml
   acoustic: dsmain/acoustic.patched.onnx
   ```

⚠️ **Warning: a uv-marked acoustic must be used with THIS vocoder.**
Rendering it with other vocoders (nsf_hifigan etc.) renders the markers
as sound — you will hear a **high-pitched beep noise**. Back up the
original voicebank before patching, and if you switch back to another
vocoder, point dsconfig's acoustic back to the original file.

Note: the vocoder works without the patch (voicing is auto-detected
from mel) — but range extension and deterministic voicing control
require it. If you edit dsdict types later, re-run the patch on the
original acoustic (the classification table is baked in at patch time).

## Compatibility

- Works with any standard DiffSinger acoustic model that outputs
  44.1 kHz / hop 512 / 128-bin mel (openvpi convention).
- Voicing behavior: by default the vocoder decides voiced/unvoiced per frame
  from the mel. Acoustic models patched with the `uvmark` tool (phoneme
  voicing markers) get deterministic voicing control — optional.

## Training data

This vocoder was trained using the **"Multi-timbre Guide Vocal Data"** and
**"Multi-singer Singing Data"** datasets from [AI Hub](https://www.aihub.or.kr)
(aihub.or.kr), built with the support of the Ministry of Science and ICT (MSIT)
of Korea and the National Information Society Agency (NIA).

## Known limitations · Roadmap (v2.1)

- Millisecond-scale plosive (k/t) attack edges are still softened by
  frame resolution — position-conditioning planned for v2.1.
- Sibilants (s/sh) may sound slightly soft vs reference.
- Further size reduction planned (fp16, ~70 MB).

---

(c) 2026 TABI — see LICENSE.txt. 한국어 안내는 README.ko.md 참고.
