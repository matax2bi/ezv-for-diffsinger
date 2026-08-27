# ezv_for_diffsinger v2.0 — Release Notes

**한국어** | [English below](#english)

OpenUtau + DiffSinger 보이스뱅크용 **피치 제어(PC) 뉴럴 보코더**의 정식 릴리스입니다.
44.1kHz / hop 512 / 128빈 mel — 표준 DiffSinger 보코더 자리에 그대로 꽂아 쓸 수 있고,
CPU만으로 실시간보다 빠르게 렌더됩니다.

> 아키텍처 코드명: **pc-ansf-next** · EZSing 프로젝트의 보코더
> 프리웨어 — 렌더한 음원은 **상업적 이용 포함 로열티 프리**. 보코더 자체의
> 판매·동봉·재배포·수정은 금지 (LICENSE.txt 참고)

---

## 🎧 데모

- `v2.0_highlight.mp4` — 30초 하이라이트 (**본문 인라인용**, 10MB 이하)
- `v2.0_demo.mp4` — 풀 렌더 데모 (**믹싱 포함** 완성본, 릴리즈 자산)
- `v2.0_demo2.mp4` — 같은 곡의 **보코더 출력 원음** (무가공, 릴리즈 자산)

> GitHub 본문 인라인 임베드는 10MB 제한 — 하이라이트만 본문에 드래그하고,
> 풀 데모 2종은 릴리즈 자산으로 첨부하세요.

## 다운로드

| 파일 | 용도 |
|---|---|
| `ezv_for_diffsinger.oudep` | **권장** — OpenUtau 창에 드래그&드롭으로 설치 끝 |
| `ezv_for_diffsinger_v2.0.zip` | 수동 설치용 (Dependencies 폴더에 압축 해제) |

설치 후 보이스뱅크 `dsconfig.yaml`에 `vocoder: ezv_for_diffsinger` 한 줄이면 됩니다.
자세한 설치·보이스뱅크 세팅(음역 확장/유무성 제어 패치)은 README.ko.md 를 참고하세요.

## v2.0 하이라이트

**엔진을 바닥부터 다시 만들었습니다.**

- **네이티브 그리드 파이프라인** — 모델 전체가 어쿠스틱과 같은 프레임
  그리드에서 동작합니다. v1.x의 내부 리샘플 층과 그로 인한 아티팩트가
  통째로 사라졌고, 렌더가 약 2배 가벼워졌습니다.
- **짧은 노트 잡음 해결** — 거센 무성자음(k/t/p/ch/ts) 앞 짧은 노트에서
  나던 잡음의 원인을 찾아 제거했습니다.
- **무음이 진짜 무음이 됩니다** — 모음과 자음 사이 폐쇄 구간의 히스를
  학습 단계에서 해결했습니다 (어쿠스틱 모델이 남기는 불완전 무음을
  보코더 체인이 스스로 정리).
- **숨성분 정확도** — 배음 사이 노이즈 바닥을 실제 목소리 기준으로 직접
  학습해, 무보정 상태로 레퍼런스와 0.3dB 이내까지 맞췄습니다. 도약
  구간의 과한 숨, 자음 어택 앞 대비도 내장 보정으로 다듬었습니다.
- **고음 지속음 안정성** — 위상 엔진을 재설계해 긴 렌더·고음에서도
  음정이 미세하게 떨리지 않습니다.
- **조음 표현의 기반** — ㄴ/ㅁ류 자음의 혀·입술 릴리즈를 표현하는 새
  경로가 내장됐고, 업데이트를 통해 계속 성숙합니다.
- **30% 경량화** — 패키지 157MB → **127MB**.

## v1.x/베타에서 업그레이드하는 경우

1. 기존 `ezv_for_diffsinger` 의존성을 새 버전으로 교체
2. **OpenUtau 렌더 캐시를 비우세요** (`도구 → 캐시 지우기`) — 구판 렌더
   재사용 방지
3. uv 마크 패치를 쓰던 보이스뱅크는 그대로 호환됩니다

⚠️ uv 마크가 적용된 어쿠스틱은 반드시 이 보코더와 함께 사용하세요 —
다른 보코더로 렌더하면 마커가 삐 소리로 들립니다 (README 참고).

## 알려진 한계 · 로드맵 (v2.1)

- 파열음(k/t)의 수 ms급 어택 타임은 프레임 해상도 한계로 아직 부드럽게
  표현됩니다 — v2.1에서 위치 조건화 기법으로 개선 예정
- 치찰음(s/sh)이 기준 대비 약간 얇게 들릴 수 있음
- 파일 추가 경량화(fp16, ~70MB) 예정

## 학습 데이터

이 보코더는 과학기술정보통신부와 한국지능정보사회진흥원(NIA)의 지원으로
구축된 [AI허브](https://www.aihub.or.kr)의 **「다음색 가이드보컬 데이터」**와
**「다화자 가창 데이터」**를 활용하여 학습되었습니다.

---

<a name="english"></a>
# ezv_for_diffsinger v2.0 — Release Notes (English)

Stable release of the **pitch-controllable neural vocoder** for
OpenUtau + DiffSinger voicebanks. 44.1 kHz / hop 512 / 128-bin mel —
drop-in compatible with the standard DiffSinger vocoder interface,
renders faster than realtime on CPU.

> Architecture codename: **pc-ansf-next** · the vocoder of the EZSing project
> Freeware — rendered audio is **royalty-free incl. commercial use**.
> The vocoder itself may not be sold, bundled, redistributed, or modified
> (see LICENSE.txt).

## 🎧 Demo

- `v2.0_highlight.mp4` — 30s highlight (**for inline embed**, under 10 MB)
- `v2.0_demo.mp4` — full render demo (**with mixing**, release asset)
- `v2.0_demo2.mp4` — same song, **raw vocoder output** (unprocessed, release asset)

## Download

- `ezv_for_diffsinger.oudep` — **recommended**; drag & drop onto OpenUtau
- `ezv_for_diffsinger_v2.0.zip` — manual install

Then set `vocoder: ezv_for_diffsinger` in your voicebank's `dsconfig.yaml`.
See README.md for full install & voicebank setup (range-extension /
voicing-control patch).

## Highlights

- **Native-grid pipeline** — the whole model now runs on the acoustic
  model's frame grid; the v1.x internal resampling layer and its
  artifacts are gone, and rendering got ~2× lighter.
- **Short-note noise fixed** — noise on short notes before aspirated
  consonants (k/t/p/ch/ts) is resolved at the root.
- **True silence** — hiss in closure gaps between vowel and consonant is
  gone; the chain learns to render imperfect silence from acoustic
  models as real silence.
- **Breath accuracy** — the inter-harmonic noise floor is trained
  directly against reference voices (within 0.3 dB, uncorrected), with
  built-in shaping for pitch-leap breath and pre-consonant contrast.
- **High-note stability** — redesigned phase engine: no micro-detune on
  long renders or sustained high notes.
- **Groundwork for articulation** — a new release-transient path
  (n/m-type tongue/lip release), maturing in updates.
- **30% smaller** — 157 MB → **127 MB**.

## Upgrading from v1.x/beta

Replace the dependency, then **clear OpenUtau's render cache**
(`Tools → Clear Cache`). uv-marked voicebanks remain compatible —
and must be rendered with THIS vocoder (other vocoders render the
markers as a beep; see README).

## Known limitations · Roadmap (v2.1)

- Millisecond-scale plosive (k/t) attack edges are still softened by
  frame resolution — position-conditioning planned for v2.1
- Sibilants (s/sh) may sound slightly soft vs reference
- Further size reduction planned (fp16, ~70 MB)

## Training data

Trained on the **"Multi-timbre Guide Vocal Data"** and **"Multi-singer
Singing Data"** datasets from [AI Hub](https://www.aihub.or.kr), built
with the support of MSIT Korea and NIA.

---

(c) 2026 TABI — see LICENSE.txt
