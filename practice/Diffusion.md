# Denoising Diffusion Probabilistic Models (DDPM) 논문 리뷰

<br>

## 1. 논문 개요

- **제목**: Denoising Diffusion Probabilistic Models  
- **저자**: Jonathan Ho, Ajay Jain, Pieter Abbeel  
- **링크**: [arXiv:2006.11239](https://arxiv.org/abs/2006.11239)  
- **발표년도**: 2020

본 논문은 **확률적 노이즈 제거(Denoising) 과정을 기반으로 하는 생성 모델**, Diffusion 모델을 제안하였다. 이 모델은 입력 이미지에 점진적으로 노이즈를 추가한 뒤, 이를 다시 제거하는 방식으로 고품질의 이미지를 생성하며, 다양한 생성 모델과 이론적으로 연결되는 구조를 갖는다.

<br>

## 2. Diffusion 모델 구조

```text
   Forward process (q): x₀ → x₁ → x₂ → ... → x_T  (노이즈 추가)
   Reverse process (p): x_T → x_{T-1} → ... → x₀  (노이즈 제거)
```

- **Forward process**: 원본 이미지에 점진적으로 가우시안 노이즈를 추가하여 \( x_T \)라는 거의 완전한 노이즈 상태에 도달
- **Reverse process**: 학습된 모델이 \( x_T \)에서부터 점진적으로 노이즈를 제거해 \( x_0 \)을 복원
- **모델 목표**: 각 시간 \( t \)마다 노이즈 성분 \( \epsilon \)을 예측하여 제거하는 함수 \( \epsilon_\theta(x_t, t) \)를 학습

<br>

## 3. 학습 목표

- Forward 과정에서 만든 노이즈가 섞인 이미지 \( x_t \)로부터 원래 섞였던 노이즈 \( \epsilon \)을 예측
- 예측값이 실제 노이즈와 가까워지도록 **MSE 기반 손실 함수**를 사용하여 학습:

```math
L_{\text{simple}}(\theta) = \mathbb{E}_{t, x_0, \epsilon} \left[ \left\| \epsilon - \epsilon_\theta\left( \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \epsilon, t \right) \right\|^2 \right]
```

- 학습된 모델은 이후 역방향 과정에서 샘플링에 활용됨

<br>

## 4. 수학적 정의

- **Forward 과정**:  
  각 시점 \( t \)에서 노이즈를 섞는 분포는 다음과 같음  
```math  
q(x_t \mid x_{t-1}) = \mathcal{N}(x_t; \sqrt{1 - \beta_t} x_{t-1}, \beta_t I)
```

- **역방향 과정**:  
  학습된 모델을 통해
```math  
  p_\theta(x_{t-1} \mid x_t) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t, t), \Sigma_\theta(x_t, t))
  ```  
  형태의 정규분포를 예측

- 전체 변분 경계(ELBO)를 기반으로 훈련 가능하지만, 실제로는 간단한 MSE 손실을 사용하여 안정적인 학습 가능

<br>

## 5. 장점

- **샘플 품질이 매우 우수**하며, 비지도 생성임에도 Conditional 모델과 유사하거나 능가하는 성능
- **학습 안정성**이 GAN보다 뛰어남 (모드 붕괴 없음)
- 이미지 외에도 **오디오, 비디오, 텍스트 등 다양한 도메인에 확장 가능**
- 여러 다른 생성 모델 (EBM, Score Matching, Autoregressive, Compression 등)과 이론적으로 연결됨

<br>

## 6. 단점

- **샘플링 속도가 느림**: 1000단계 이상의 역방향 반복이 필요함
- 학습 시 GPU 자원 사용량이 많고, **고비용**
- Diffusion 과정이 **설명적으로 복잡**하고 직관적이지 않음 (특히 Forward의 역할 이해가 어려울 수 있음)

<br>

## 7. 결론

DDPM은 노이즈 추가와 제거라는 단순한 구조를 통해 고품질 이미지를 생성하는 강력한 프레임워크이다. 이 모델은 수학적으로 다양한 기존 생성 모델들과 연결되며, 손실 압축 관점에서도 우수한 성능을 보인다. 특히, 노이즈 예측이라는 단순한 학습 목표로도 SOTA에 가까운 결과를 낼 수 있다는 점에서 향후 다양한 생성 모델의 기반이 될 수 있다.

<br>

## 참고 문헌

- [arXiv:2006.11239](https://arxiv.org/abs/2006.11239)  
- Ho, Jonathan, et al. "Denoising diffusion probabilistic models." *NeurIPS* (2020).