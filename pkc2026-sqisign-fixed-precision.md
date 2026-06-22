# SQIsign with Fixed-Precision Integer Arithmetic

**Venue:** PKC 2026 (Best Paper Award)  
**Authors:** Won Kim, Jeonghwan Lee, Hyeonhak Kim, Changmin Lee  
**ePrint:** https://eprint.iacr.org/2025/1621

---

## Problem Statement

SQIsign은 아이소제니 기반 서명 방식으로, 내부적으로
사원수 대수(quaternion algebra)의 ideal 연산을 수행함.

기존 구현의 문제: 사원수 계수가 ℚ(유리수) 위에 있어서
연산 중 분자가 폭발적으로 커짐.

- GMP 같은 다중정밀도 라이브러리 필요
- 임베디드 환경 배포 불가
- 상수시간(constant-time) 구현 어려움 → 사이드채널 공격 취약

이 논문의 핵심 질문:
**"사원수 계수의 크기에 명시적 worst-case bound를 줄 수 있는가?"**

---

## SQIsign 서명 흐름 (배경)

SQIsign의 Sign은 크게 세 단계로 구성:
KeyGen:

비밀키 sk = 초특이 타원곡선 E₀에서 E_A로의 아이소제니 τ

공개키 pk = E_A
Sign(sk, msg):

랜덤 commitment 곡선 E₁ 선택 (E₀ → E₁ 아이소제니 ψ)
challenge c = H(E_A, E₁, msg) 계산
KLPT 알고리즘으로 response ideal I 계산

→ I는 사원수 대수 O₀의 ideal

→ I의 계수가 ℚ 위에서 폭발적으로 증가하는 지점
I에서 아이소제니 σ: E₁ → E_A 복원

서명 σ = (E₁, σ)

Verify(pk, msg, σ):

E_A와 E₁이 일치하는지 확인

→ 3단계 KLPT에서 사원수 계수가 GMP 없이는 표현 불가능했음.

---

## 핵심 기여: Worst-Case Bound 도출

KLPT 알고리즘 내부의 IdealMultiplication을 분석해서
사원수 계수의 **명시적 uniform worst-case bound** 확립.

**기존:** LLL-reduced basis 가정 필요 → bound 계산 가능하지만 조건부  
**이 논문:** LLL-reduced 조건 없이도 → tight closed-form bound

구체적으로, 사원수 원소 α = a + bi + cj + dk에 대해
Round-2 SQIsign 전체 루틴에서 |a|, |b|, |c|, |d|의
worst-case 상계를 각 루틴별로 명시적으로 계산.

→ 필요한 limb 수를 사전에 고정 가능
→ 64-bit limb 기준 fixed-precision C 구현 달성

---

## 사원수 대수와 격자의 관계

SQIsign이 복잡한 이유: 세 가지 수학이 혼재
타원곡선 (아이소제니)

↕ Deuring correspondence

사원수 대수 (ideal 연산)

↕ ℤ-module 구조

격자 (LLL 축소)

Order O = ℤ·1 + ℤ·i + ℤ·j + ℤ·k 가 rank-4 격자를 형성.  
ideal basis를 짧게 만드는 것 = 격자 축소 문제 → LLL 사용.

LLL이 여기서 하는 역할: 격자 암호에서의 "공격 도구"와 달리,
사원수 ideal basis를 정리하는 **내부 계산 도구**로 사용됨.

---

## SQIsign vs SQIsignHD

| | SQIsign | SQIsignHD |
|---|---|---|
| 계산 방식 | 1차원 아이소제니 체인 | 차원-4 아이소제니 (Kani's Lemma) |
| 핵심 도구 | KLPT | Kani's Lemma |
| 서명 속도 | 느림 | 빠름 |
| 공개키/서명 크기 | 가장 작음 | 중간 |
| fixed-precision 문제 | 이 논문에서 해결 | **아직 미해결** |

→ SQIsignHD는 차원-4 구조로 인해 사원수 계수 bound 분석이
훨씬 복잡하며, fixed-precision 전환이 별도 연구로 남아있음.

---

## 핵심 수치

**구현 결과 (논문 기준):**
- GMP 완전 제거: 64-bit fixed-precision 정수만으로 SQIsign 구현
- 각 루틴별 limb 수를 worst-case bound로 사전 고정
- constant-time 구현 및 임베디드 배포 가능성 확보

**후속 논문 (ePrint 2026/1031, PKC 2026 한 달 후):**
- "Compact Quaternion Algorithms for SQIsign" — Won Kim, Changmin Lee, Hyunwoo Yoo
- 이 논문의 precision budget이 공개키 크기의 **13배 이상**이라는 한계를 지적
- 사원수 알고리즘 자체를 수정해서 precision budget을 대폭 압축하는 방향 제시

---

## 한계 및 열린 문제

**1. Precision budget이 여전히 큼**  
이 논문의 worst-case bound는 보수적(conservative)이어서
실제 필요한 것보다 훨씬 큰 버퍼를 예약함.
공개키 크기의 13배 이상 → 임베디드 환경에서 메모리 부담.
후속 논문(ePrint 2026/1031)이 이를 개선하는 방향으로 나옴.

**2. SQIsignHD fixed-precision 미해결**  
이 논문은 1차원 SQIsign만 다룸. SQIsignHD(차원-4)는
Kani's Lemma 기반 구조로 인해 사원수 계수 분석이 훨씬 복잡하며,
동일한 방법론을 적용할 수 있는지 불명확.

**3. Constant-time 완전 달성 여부**  
fixed-precision이 constant-time의 **필요조건**이지 충분조건이 아님.
GMP 제거 이후에도 분기문, 메모리 접근 패턴 등 별도 취약점이 남아있음.
(참고: ePrint 2025/832에서 SQIsign constant-time 구현 별도 연구)

---

## 개인 메모

- LLL이 SQIsign에서 격자 암호의 "공격 도구"와 반대 역할을 한다는 점이
  흥미로움: 같은 수학 도구가 맥락에 따라 방어/공격으로 나뉨
- fixed-precision 전환이 표준화의 핵심 병목이었음:
  NIST Round-2에서 SQIsign이 임베디드 배포 불가 지적을 받은 이유가 바로 이것
- SQIsignHD의 fixed-precision 문제가 아직 열려있다는 점에서
  이 방향이 단기 연구 주제로 유효할 수 있음
- 이 논문 저자(Changmin Lee)가 S&P 2026 approximate hints 저자이기도 함:
  같은 교수님이 격자(cryptanalysis)와 아이소제니(implementation) 양쪽을 다룬다는 점에서
  PQSnC Lab의 연구 폭이 넓음을 확인

---

## References

- [EHLM24] De Feo et al., "SQIsignHD", EUROCRYPT 2024
- [DFLZ23] De Feo et al., "SQIsign", ASIACRYPT 2023
- [ePrint 2026/1031] Kim, Lee, Yoo, "Compact Quaternion Algorithms for SQIsign"
- [ePrint 2025/832] Kouider et al., "Constant-time Integer Arithmetic for SQIsign"
- [ePrint 2026/394] De Feo et al., "SQISign on ARM", 2026
