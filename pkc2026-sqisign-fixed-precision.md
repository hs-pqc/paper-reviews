# SQIsign with Fixed-Precision Integer Arithmetic
**Venue:** PKC 2026 (Best Paper Award)  
**Authors:** Won Kim, Jeonghwan Lee, Hyeonhak Kim, Changmin Lee  
**ePrint:** https://eprint.iacr.org/2025/1621

---

## Problem Statement

SQIsign은 아이소제니 기반 서명 방식으로, 내부적으로
사원수 대수(quaternion algebra)의 ideal 연산을 수행함.

기존 구현의 문제:
사원수 계수가 ℚ(유리수) 위에 있어서

연산 중 분자가 폭발적으로 커짐

→ GMP 같은 다중정밀도 라이브러리 필요

→ 임베디드 환경 배포 불가

→ 상수시간(constant-time) 구현 어려움

→ 사이드채널 공격 취약

---

## 핵심 기여

KLPT 알고리즘 내부의 IdealMultiplication을 수정해서
사원수 계수의 **명시적 worst-case 상계(bound)** 를 도출.
기존: LLL-reduced basis 가정 필요 → bound 계산 가능

이 논문: LLL-reduced 조건 없이도 → tight closed-form bound

→ fixed-precision 정수 연산만으로 SQIsign 구현 가능

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

---

## SQIsign vs SQIsignHD

| | SQIsign | SQIsignHD |
|--|---------|-----------|
| 계산 방식 | 1차원 아이소제니 체인 | 차원-4 아이소제니 |
| 핵심 도구 | KLPT | Kani's Lemma |
| 속도 | 느림 | 빠름 |
| 구현 복잡도 | 상대적으로 단순 | 복잡 |

이 논문은 SQIsign(1차원) 구현 문제를 해결.
SQIsignHD는 fixed-precision 문제가 아직 더 많이 남아있음.

---

## 후속 연구

PKC 2026 이후 한 달 만에 후속 논문 등장:
- "Compact Quaternion Algorithms for SQIsign" (ePrint 2026/1031)
- precision budget을 공개키 크기의 13배 이하로 압축하는 방향

---

## 개인 메모

- LLL이 SQIsign에서 하는 역할: 공격 도구(격자 암호)와 다르게
  사원수 ideal basis를 정리하는 내부 도구로 사용됨
- 4차원이 나오는 이유: 초특이 타원곡선의 End(E) ≅ 사원수 대수
  → ℤ-module로 보면 rank-4 격자
- fixed-precision 전환이 표준화와 임베디드 배포의 핵심 병목이었음

---

## References

- [EHLM24] De Feo et al., "SQIsignHD", EUROCRYPT 2024
- [DFLZ23] De Feo et al., "SQIsign", ASIACRYPT 2023
- [ePrint 2026/1031] Compact Quaternion Algorithms for SQIsign
