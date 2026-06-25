# FIPS 203: Module-Lattice-Based Key-Encapsulation Mechanism (ML-KEM)

**Type:** NIST Standard  
**Published:** August 2024  
**Based on:** CRYSTALS-Kyber

---

## 1. Overview

ML-KEM is a Key Encapsulation Mechanism (KEM) based on Module-LWE hardness.
Standardized by NIST in August 2024 as FIPS 203.
Provides IND-CCA2 security via Fujisaki-Okamoto (FO) transform.

---

## 2. Parameter Sets

| Parameter | n | k | q | η1 | η2 | du | dv | Security |
|---|---|---|---|---|---|---|---|---|
| ML-KEM-512 | 256 | 2 | 3329 | 3 | 2 | 10 | 4 | Level 1 (128-bit) |
| ML-KEM-768 | 256 | 3 | 3329 | 2 | 2 | 10 | 4 | Level 3 (192-bit) |
| ML-KEM-1024 | 256 | 4 | 3329 | 2 | 2 | 11 | 5 | Level 5 (256-bit) |

실제 격자 차원: n × k (512, 768, 1024)

---

## 3. Core Algorithms

### K-PKE (내부 PKE)
- KeyGen: A ← R_q^(k×k), s,e ← CBD(η1), t = As + e
- Encrypt: r,e1,e2 ← CBD(η1/η2), u = A^T r + e1, v = t^T r + e2 + m
- Decrypt: m = v - s^T u

### ML-KEM (FO Transform 적용)
- KeyGen → (ek, dk)
- Encaps → (K, c): 랜덤 m 생성 후 K,r = H(m,H(ek)), c = K-PKE.Enc(ek,m,r)
- Decaps → K: m' = K-PKE.Dec(dk,c), 검증 후 K 반환

---

## 4. NTT 구현

q=3329, n=256에서 NTT 사용.
ω = 17 (primitive 512th root of unity mod 3329)
7단계 NTT (base_mul로 마지막 레이어 처리)

직접 구현:
- Barrett reduction: 나눗셈을 곱셈으로 대체
- Montgomery reduction: 비트 시프트 기반, 2배 빠름
- int16_t overflow 버그 경험 → int32_t 캐스팅으로 해결

---

## 5. Security Analysis

MATZOV cost model 기준:
- ML-KEM-512: 139.7비트 (마진 11.7비트)
- ML-KEM-768: 196.4비트 (마진 4.4비트) ← 가장 작음
- ML-KEM-1024: 262.3비트 (마진 6.3비트)

**핵심 발견:** ML-KEM-768 마진이 FFT distinguisher 구현 가능성에 따라
4.4비트(FFT 가능)와 14.4비트(FFT 불가)로 10비트 차이남.

---

## 6. 연결된 연구

- lattice-estimator 실험: hs-pqc/lattice-estimator-mlkem
- approximate hints: Lee et al., S&P 2026
- NTT 구현: hs-pqc/ml-kem-ntt
