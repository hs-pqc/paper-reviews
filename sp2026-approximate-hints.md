# From Perfect to Approximate Hints
**Venue:** IEEE S&P 2026  
**Authors:** Minki Hhan, Ga Hee Hong, Jiseung Kim, Changmin Lee, JeongHwan Lee  
**ePrint:** https://eprint.iacr.org/2026/1081

---

## Problem Statement

LWE 기반 암호에서 sparse secret(비밀키에 0이 많은 경우)을 
사용할 때, side-channel 등으로 얻은 힌트가 얼마나 공격에 
도움이 되는지를 분석한 논문.
Perfect Hint:  ⟨s, v⟩ = l        (정확한 내적값)

Approximate Hint: ⟨s, v⟩ ≈ l    (근사적인 내적값)

기존 Perfect Hints 연구(DDGR, CRYPTO 2020)는 가정이 너무 강해서 
현실적이지 않았음. 이 논문은 Approximate Hints로 완화해도 
공격이 충분히 강력함을 보임.

---

## Key Idea

### Sparse Secret의 취약성

비밀키 s의 Hamming weight가 낮을수록(0이 많을수록) 취약해지는 이유:

1. **추측 공격**: 0인 위치를 맞추면 격자 차원 감소
   → dual hybrid에서 p, ζ 최적화에 유리

2. **Hints 효과 증폭**: sparse secret은 이미 분포가 좁음
   → Approximate hint 하나의 정보량이 커짐
일반 secret + hint  → 분포가 조금 좁아짐

Sparse secret + hint → 분포가 훨씬 더 좁아짐

### ML-KEM과의 연관성

ML-KEM은 중심이항분포(CBD) 사용:
η=2: 계수가 0일 확률 ≈ 38%

η=3: 계수가 0일 확률 ≈ 31%
→ sparse secret 시나리오가 현실적으로 적용 가능

---

## 공격 흐름
Side-channel 등으로 Approximate Hints 수집

↓

Hints를 LWE 격자에 통합

↓

Low Hamming Weight 조건 활용

↓

Dual Hybrid Attack으로 비밀키 복원

---

## 관련 연구 흐름
DDGR (CRYPTO 2020)    → Perfect Hints + DBDD 프레임워크

Too Many Hints (2023)  → Perfect/Modular Hints + LLL

이 논문 (S&P 2026)    → Approximate Hints + Low Hamming Weight

---

## 개인 메모

- lattice-estimator 실험에서 dual_hybrid가 primal보다 효율적인 것과 연결됨
- ML-KEM-768에서 ζ=20 anomaly가 sparse secret 특성과 관련 있을 수 있음
- Approximate hint의 현실적 시나리오: 전력 분석, 타이밍 공격, 오류 주입

---

## References

- [DDGR20] Dachman-Soled et al., "LWE with Side Information", CRYPTO 2020
- [MN23] May & Nowakowski, "Too Many Hints", 2023
- [FIPS 203] ML-KEM Standard, NIST 2024
