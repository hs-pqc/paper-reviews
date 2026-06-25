# FIPS 204: Module-Lattice-Based Digital Signature Algorithm (ML-DSA)

**Type:** NIST Standard  
**Published:** August 2024  
**Based on:** CRYSTALS-Dilithium

---

## 1. Overview

ML-DSA는 Module-LWE/SIS 기반 디지털 서명 알고리즘.
NIST FIPS 204로 2024년 8월 표준화.
Fiat-Shamir with Aborts 패러다임 사용.

---

## 2. Parameter Sets

| Parameter | n | k | l | q | η | Security |
|---|---|---|---|---|---|---|
| ML-DSA-44 | 256 | 4 | 4 | 8380417 | 2 | Level 2 (128-bit) |
| ML-DSA-65 | 256 | 6 | 5 | 8380417 | 4 | Level 3 (192-bit) |
| ML-DSA-87 | 256 | 8 | 7 | 8380417 | 2 | Level 5 (256-bit) |

q = 8380417 = 2^23 - 2^13 + 1 (NTT 친화적 소수)

---

## 3. Core Algorithms

### KeyGen
- A ← R_q^(k×l), s1 ← S_η^l, s2 ← S_η^k
- t = As1 + s2
- 출력: (pk=(A,t), sk=(A,t,s1,s2))

### Sign (Fiat-Shamir with Aborts)
- y ← S_γ1^l 랜덤 샘플링
- w = Ay, w1 = HighBits(w)
- c = H(μ, w1) (challenge)
- z = y + cs1
- 조건 불만족시 abort → 재시도
- 출력: σ = (z, h)

### Verify
- w' = Az - ct 복원
- c' = H(μ, HighBits(w')) 검증

---

## 4. ML-KEM과의 차이

| | ML-KEM | ML-DSA |
|---|---|---|
| 목적 | KEM | 서명 |
| q | 3329 | 8380417 |
| 보안 기반 | MLWE | MLWE + MSIS |
| 핵심 기법 | FO transform | Fiat-Shamir with Aborts |

---

## 5. Security Analysis

lattice-estimator 실험 결과:
- ML-DSA-44: primal 143.5비트, dual 144.3비트
- ML-DSA-65: primal 231.3비트, dual 233.9비트
- ML-DSA-87: primal 302.4비트, dual 304.2비트

세 파라미터셋 모두 primal이 dual보다 효율적.
q=8380417로 커서 ML-KEM과 다른 보안 구조.

---

## 6. 연결된 연구

- Fiat-Shamir with Aborts: abort 확률과 보안의 trade-off
- lattice-estimator 실험: hs-pqc/lattice-estimator-mlkem
- MSIS 기반 서명 보안 분석
