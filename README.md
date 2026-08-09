# ezv_for_diffsinger (BETA)

**English** | [한국어](README.ko.md)

A **pitch-controllable neural vocoder** for OpenUtau + DiffSinger voicebanks.
44.1 kHz / hop 512 / 128-bin mel — drop-in compatible with the standard
DiffSinger vocoder interface.

> BETA release. Planned to become the vocoder of the **EZSing** project.
> Freeware — **rendered audio is royalty-free, commercial use OK.**
> The vocoder itself: no selling/bundling, no redistribution, no
> modification. See `LICENSE.txt`.

## What it does

- **True pitch control (PC)**: harmonics are generated directly from the f0
  curve, so pitch stays accurate even far outside the voicebank's recorded
  range. Combined with the
  [diffsinger-pitch-limit](https://github.com/matax2bi/diffsinger-pitch-limit)
  tool, any voicebank can sing high notes with a healthy timbre.
- **CPU realtime**: renders faster than realtime on CPU (RTF ≈ 0.8) — no GPU
  required (`force_on_cpu` is set on purpose; the graph uses fp64 ops that
  some DirectML devices lack).
- Built-in protection against momentary loudness spikes (transparent to
  sustained dynamics).

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
3. Render. If the voicebank's acoustic model is pitch-limited with the
   pitch-limit tool, out-of-range notes will keep in-range timbre while the
   vocoder renders the true pitch.

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
  from the mel. Acoustic models patched with the `uvmark` tool (3-way
  phoneme voicing markers) get deterministic voicing control — optional.

## Known beta limitations

- Breathiness may sound slightly dry on some voices (tuning in progress).
- Very low male voices may lose some brightness in the 4-8 kHz range.
- Feedback is welcome — this is a beta.

---

(c) 2026 TABI — see LICENSE.txt. 한국어 안내는 README.ko.md 참고.
