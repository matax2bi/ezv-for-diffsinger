# ezv_for_diffsinger (베타)

[English](README.md) | **한국어**

OpenUtau + DiffSinger 보이스뱅크용 **피치 제어(PC) 뉴럴 보코더**입니다.
44.1kHz / hop 512 / 128빈 mel — 표준 DiffSinger 보코더 인터페이스에 그대로
꽂아 쓸 수 있어요.

> 베타 배포입니다. 이 보코더는 향후 **EZSing** 프로젝트의 보코더가 될
> 예정입니다. 프리웨어 — **렌더한 음원은 로열티 프리, 상업적 이용 가능.**
> 보코더 자체는 판매·동봉·재배포·수정 금지. 자세한 내용은 `LICENSE.txt`.

## 특징

- **진짜 피치 제어**: 배음을 f0 곡선에서 직접 합성하므로 보이스뱅크의
  녹음 음역을 크게 벗어나도 음정이 정확합니다.
  [diffsinger-pitch-limit](https://github.com/matax2bi/diffsinger-pitch-limit)
  도구와 함께 쓰면 어떤 보이스뱅크든 건강한 음색으로 고음을 낼 수 있어요.
- **CPU 실시간**: GPU 없이 CPU만으로 실시간보다 빠르게 렌더됩니다
  (RTF ≈ 0.8). `force_on_cpu`는 의도된 설정이에요 (그래프의 fp64 연산이
  일부 DirectML 장치에서 미지원).
- 순간적인 음량 스파이크 방어 내장 (지속적인 다이내믹스에는 영향 없음).

## 설치 (OpenUtau)

**가장 쉬운 방법 — .oudep 드래그&드롭:**
`ezv_for_diffsinger.oudep` 파일을 OpenUtau 창에 끌어다 놓으세요
(또는 `도구 → 의존성 설치` 메뉴). 끝 — 아래 2번으로 바로 가면 됩니다.

**수동 설치 (zip):**
1. `ezv_for_diffsinger` 폴더를 OpenUtau의 `Dependencies` 폴더에 복사:
   `OpenUtau/Dependencies/ezv_for_diffsinger/`
   (폴더 안에 `ezv_for_diffsinger.onnx`와 `vocoder.yaml`이 있어야 합니다)
2. 사용할 보이스뱅크의 `dsconfig.yaml`에서:
   ```yaml
   vocoder: ezv_for_diffsinger
   ```
3. 사용준비완료! 어쿠스틱에 한계음 패치가 적용돼 있으면, 음역 밖 노트도
   음역 안 음색을 유지한 채 보코더가 실제 음정으로 렌더합니다.

## 사용법 (보이스뱅크 세팅)

보코더의 유/무성 제어와 음역 확장 기능을 제대로 쓰려면 보이스뱅크의
어쿠스틱을 한 번 패치해야 합니다.
[diffsinger-pitch-limit](https://github.com/matax2bi/diffsinger-pitch-limit)
도구를 받아 아래 순서로 진행하세요.

1. **dsdict 수정** — 보이스뱅크 dsdict.yaml 의 `symbols:` 타입을
   음소 성질에 맞게 정리합니다:
   - 무성자음 → `stop` (fricative/affricate/aspirate 도 인식됩니다)
   - 항상 유성인 자음(비음·유음 등) → `nasal`, `liquid`, `voiced`
   - 위치에 따라 유/무성이 달라지는 자음(예: 한국어 평음 ㄱ·ㄷ·ㅂ·ㅈ)
     → `lenis` (보코더가 프레임별로 스스로 판단)
2. **어쿠스틱 패치** — diffsinger-pitch-limit 의 bat 파일에 어쿠스틱
   onnx 를 드래그&드롭 → 음역 한계(상한/하한)를 지정하고, uv 마크
   질문에 `y` 로 실행합니다.
3. **dsconfig 수정** — 패치된 어쿠스틱 onnx 가 생성됐는지 확인한 뒤,
   보이스뱅크 `dsconfig.yaml` 의 acoustic 항목을 바꿉니다:
   ```yaml
   acoustic: dsmain/acoustic.patched.onnx
   ```

⚠️ **주의: uv 마크가 적용된 어쿠스틱은 반드시 이 보코더와 함께
사용하세요.** 다른 보코더(nsf_hifigan 등)로 렌더하면 마커가 소리로
그대로 렌더돼 **삐 소리(고음 노이즈)가 납니다.** 패치 전에 원본
보이스뱅크를 백업해두고, 다른 보코더로 되돌릴 땐 dsconfig 의
acoustic 을 원본으로 되돌리세요.

※ 패치 없이도 보코더는 동작합니다 (유/무성은 mel 에서 자동 판단) —
다만 음역 확장과 결정적 유/무성 제어는 패치가 있어야 활성화됩니다.
※ dsdict 타입을 나중에 수정했다면, 원본 어쿠스틱에서 패치를 다시
실행해야 반영됩니다 (분류표가 패치 시점에 onnx 에 저장되기 때문).

## 호환성

- 44.1kHz / hop 512 / 128빈 mel(openvpi 규약)을 출력하는 표준 DiffSinger
  어쿠스틱이면 모두 호환.
- 유/무성 판정: 기본적으로 보코더가 mel에서 프레임별로 스스로 판단합니다.
  `uvmark` 도구로 패치된 어쿠스틱(3분류 음소 마커)은 결정적 유무성 제어를
  받습니다 — 선택 사항이에요.

## 베타 알려진 한계

- 일부 목소리에서 숨성분이 약간 건조하게 들릴 수 있음 (튜닝 중)
- 아주 낮은 남성 음역에서 4-8kHz 선명도가 다소 감소할 수 있음
- 피드백 환영합니다 — 베타예요.

---

(c) 2026 TABI — LICENSE.txt 참고.
