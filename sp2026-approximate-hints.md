# From Perfect to Approximate Hints

**Venue:** IEEE S&P 2026  
**Authors:** Minki Hhan, Ga Hee Hong, Jiseung Kim, Changmin Lee, JeongHwan Lee  
**ePrint:** https://eprint.iacr.org/2026/1081

---

## Problem Statement

LWE 기반 암호에서 sparse secret(비밀키 s의 Hamming weight h가 낮은 경우)을
사용할 때, side-channel 등으로 얻은 힌트가 얼마나 공격에 도움이 되는지를
분석한 논문.

- Perfect Hint: ⟨s, v⟩ = l (정확한 내적값)
- Approximate Hint: ⟨s, v⟩ = l + e, |e| ≤ β (근사적인 내적값)

기존 연구(DDGR, CRYPTO 2020)는 비밀키를 복원하는 데 약 n/2개의
perfect/modular hint가 필요했음. 이 논문은 sparse secret일 때
O(h log₂ h)개의 approximate hint만으로 충분함을 보임.

---

## DBDD 프레임워크와 Hint 통합

DBDD(Distorted Bounded Distance Decoding): LWE를 타원형 거리 함수를
가진 격자 문제로 재정식화. 비밀키 s의 사전 분포를 공분산 행렬 Σ로 표현.

**Perfect Hint 통합:**
hint ⟨s, v⟩ = l이 주어지면 hyperplane 제약이 추가됨.

    Σ' = Σ - (Σv)(Σv)ᵀ / (vᵀΣv)

격자의 유효 차원이 1 감소 → BKZ 비용 직접 감소.

**Approximate Hint 통합:**
hint ⟨s, v⟩ ≈ l (오차 분산 σ_h²)이면 차원은 줄지 않지만 분포가 좁아짐.

    Σ' = (Σ⁻¹ + vvᵀ/σ_h²)⁻¹

**Sparse secret에서 효과가 증폭되는 이유:**
일반 secret: Σ의 고유값이 크기 때문에 Σ' ≈ Σ (hint 효과 미미)
Sparse secret: Σ의 고유값이 이미 작기 때문에 Σ' << Σ (hint 효과 극대화)

→ h가 낮을수록 hint 하나당 정보량이 기하급수적으로 커짐.

---

## Key Idea

### Sparse Secret의 취약성

비밀키 s의 Hamming weight h가 낮을수록 취약한 이유:

1. **추측 공격**: 0인 위치를 조합하면 격자 차원 감소
   → dual hybrid에서 meet-in-the-middle 파라미터 최적화에 유리
2. **Hint 효과 증폭**: sparse secret은 이미 분포가 좁음
   → Approximate hint 하나의 공분산 업데이트 효과가 커짐

### ML-KEM과의 연관성

ML-KEM은 중심이항분포(CBD) 사용:
- η=2: 계수가 0일 확률 ≈ 38%
- η=3: 계수가 0일 확률 ≈ 31%

→ sparse secret 시나리오가 ML-KEM에 현실적으로 적용 가능.
단, ML-KEM의 h는 FHE 수준(h≈32)보다 훨씬 크기 때문에
직접적인 위협보다는 파라미터 마진 분석에 더 가깝다.

---

## 공격 흐름

Side-channel 등으로 Approximate Hint (v, l) 수집 — 목표: O(h log₂ h)개

↓

DBDD 프레임워크: Σ를 반복 업데이트하여 비밀키 분포를 점진적으로 좁힘

↓

Low Hamming Weight 구조 활용:
  비-제로 위치 조합 enumerate → 유효 격자 차원 h로 축소

↓

Dual Hybrid Attack: 축소된 격자에 BKZ 적용 → 비밀키 s 복원

---

## 핵심 수치 (논문 결과)

**기존 연구 대비 필요 hint 수 비교:**

| 설정 (n, h) | 기존 [23] (perfect hints) | 이 논문 (approximate hints) | 감소율 |
|---|---|---|---|
| (2¹⁵, 32) FHE 부트스트래핑 | 2¹⁴ ≈ 16,384개 | 320개 | **51배 감소** |

→ O(n/2) → O(h log₂ h) = O(32 × 5) = O(160) 수준으로 감소.

**GAA(Gaussian Approximation Assumption) 하에서의 보수적 하한:**
논문은 실험적 결과를 GAA 기반 lower-bound 분석으로 뒷받침함.
GAA: hint 통합 후 비밀키 분포를 Gaussian으로 근사하는 가정.

---

## 관련 연구 흐름

| 논문 | 핵심 기여 | 한계 |
|---|---|---|
| DDGR (CRYPTO 2020) | Perfect Hints + DBDD 프레임워크 최초 제안 | n/2개 hint 필요, approximate 미지원 |
| Too Many Hints (2023) | Perfect/Modular Hints + LLL | sparse secret 특성 미활용 |
| **이 논문 (S&P 2026)** | Approximate Hints + Low Hamming Weight | FHE 중심, ML-KEM 직접 위협은 제한적 |

---

## 한계 및 열린 문제

**1. Hint 획득 가정의 현실성**  
공격자가 품질 좋은 approximate hint(작은 β)를 얻으려면
정밀한 side-channel이 필요함. 실제 전력 분석에서 β를
얼마나 작게 만들 수 있는지는 별도 연구 필요.

**2. Modular hint와의 통합 부재**  
Too Many Hints(2023)의 modular hint와 이 논문의
approximate hint를 동시에 활용하는 통합 프레임워크가 없음.
두 종류의 hint를 혼합할 때 DBDD 업데이트가 어떻게
상호작용하는지 분석이 남아있음.

**3. ML-KEM 직접 적용의 한계**  
이 논문의 주요 타겟은 FHE(h≈32)이며, ML-KEM의 경우
η=2 기준 h ≈ n×0.38로 훨씬 크기 때문에 O(h log₂ h) hint
요구량이 여전히 현실적 위협 수준은 아님.
그러나 파라미터 마진이 생각보다 좁을 수 있다는 함의는 있음.

---

## 개인 메모

- lattice-estimator 실험에서 dual_hybrid가 primal보다 효율적인 것과 직접 연결됨:
  sparse secret일수록 dual hybrid의 meet-in-the-middle 단계가 유리해지는 구조
- ML-KEM-768에서 ζ=20 anomaly 재검토 필요:
  내 실험에서 ζ=20 근방 dual_hybrid 비용이 비정상적으로 낮게 나왔는데,
  CBD η=2의 sparse 특성과 approximate hint 조합이 이 구간에서
  특히 효과적인지 검증해볼 가치 있음
- GAA가 tight한지 여부가 이 논문 전체의 신뢰도를 결정함:
  Gaussian 근사가 실제 sparse ternary 분포와 얼마나 다른지 분석 필요

---

## References

- [DDGR20] Dachman-Soled et al., "LWE with Side Information", CRYPTO 2020
- [MN23] May & Nowakowski, "Too Many Hints", 2023
- [FIPS 203] ML-KEM Standard, NIST 2024
- [ePrint 2026/1081] Hhan et al., "From Perfect to Approximate Hints", S&P 2026
