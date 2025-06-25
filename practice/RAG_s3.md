# s3: You Don’t Need That Much Data to Train a Search Agent via RL 논문 리뷰

<br>

## 1. 논문 개요

- **제목**: s3: You Don’t Need That Much Data to Train a Search Agent via RL  
- **저자**: Patrick Lewis, Ethan Perez, Sebastian Riedel, et al.  
- **링크**: [arXiv:2505.14146](https://arxiv.org/abs/2505.14146)  
- **발표년도**: 2025  

본 논문은 검색 기반 생성 시스템(RAG)에서 생성기는 고정시키고, 검색기(Searcher)만을 강화학습(RL)으로 훈련하여 성능을 극대화하는 프레임워크인 **s3**를 제안한다. 특히, 극히 소량의 데이터만으로도 우수한 성능을 낼 수 있도록 설계되었으며, 새로운 보상 함수인 **Gain Beyond RAG(GBR)**을 통해 검색기의 훈련을 가능하게 한다.

<br>

## 2. s3 프레임워크 구조

```text
Question
  ↓
Searcher (강화학습됨)
  ├─> Query Generation → Document Retrieval
  └─> Plan: 문서가 충분한지 판단 후 반복 여부 결정
↓
All Important Docs D_s3 + Question
↓
Generator LLM (고정됨)
↓
Answer 생성
```

- **Searcher**: 반복적으로 쿼리를 생성하고 문서를 검색하며, 문서가 충분하면 종료
- **Generator**: OpenAI/Claude 등 고정된 생성기 (fine-tuning 하지 않음)
- **Plan 모듈**: 검색을 중단할 시점을 판단
- **보상(Reward)**: 생성 품질 향상 여부를 기준으로 계산된 GBR 사용

<br>

## 3. 학습 목표 (Gain Beyond RAG)

```math
\text{Gain Beyond RAG (GBR)} = \text{Accuracy}(D_{s3}, Q) - \text{Accuracy}(D_{RAG}, Q)
```

- \( D_{s3} \): s3가 선택한 문서 집합  
- \( D_{RAG} \): 기존 naive top-k 방식의 문서 집합  
- \( Q \): 원 질문, \( A \): 정답  

→ **생성 정확도가 얼마나 향상되었는지를 보상으로 환산하여 Searcher를 훈련**

<br>

## 4. 수학적 정의 요약

- **상태**: 현재 질문, 선택된 문서 목록
- **행동**: 검색 쿼리 생성 또는 종료 판단
- **보상**: 생성기 성능 향상량 (GBR)
- **정책 학습**: REINFORCE 기반 정책 최적화 사용

<br>

## 5. 장점

- **극소량 데이터로도 우수한 성능**  
  → 2,400개 샘플로도 기존 SOTA보다 높은 정확도

- **생성기와 분리된 구조**  
  → 생성기를 변경하지 않고 다양한 API와 조합 가능

- **범용성**  
  → 일반 QA, 의학 QA 등 11개 벤치마크에서 일관된 성능 향상

- **모듈화된 경량 구조**  
  → 검색기만 별도로 훈련 가능하여 학습 비용 효율적

<br>

## 6. 단점

- **강화학습의 불안정성**  
  → 보상 신호에 따라 수렴 속도가 달라짐

- **검색 종료 판단의 어려움**  
  → Plan 모듈이 잘못 판단할 경우 불완전한 문서 집합 전달 가능

- **추론 비용 증가 가능성**  
  → 반복 검색 구조로 인해 시간 증가 우려

<br>

## 7. 결론

s3는 기존 RAG 시스템의 한계를 넘어, **검색기를 강화학습 기반으로 최적화**하는 경량화된 구조를 제시한다. 생성기는 고정된 채로 유지하되, 검색기를 문서 선택 성능에 따라 학습시켜 적은 데이터로도 우수한 결과를 달성할 수 있다. LLM을 fine-tuning하지 않고도 검색 기반 QA 성능을 크게 향상시킬 수 있다는 점에서 실용성이 매우 높다.

<br>

## 8. 시사점

- **RAG의 핵심 병목은 검색기임을 실험적으로 입증**  
- **생성기는 API 사용, 검색기만 훈련**하는 경량화된 실무 파이프라인 구현 가능  
- **강화학습 기반 검색 최적화**는 LLM fine-tuning 대비 비용 효율적  
- **다중 단계 검색/응용형 검색(Task Planning)에 확장 가능**  
- 실시간 검색 + 생성 시스템, 의료/법률 QA 등에서도 유용하게 쓰일 수 있음

<br>

## 참고 문헌

- [arXiv:2505.14146](https://arxiv.org/abs/2505.14146)  
- Lewis, Patrick, et al. “s3: You Don’t Need That Much Data to Train a Search Agent via RL.” arXiv preprint arXiv:2505.14146 (2025).  