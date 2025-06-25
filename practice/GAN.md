# Generative Adversarial Nets (GAN) 논문 리뷰

<br>

## 1. 논문 개요

- **제목**: Generative Adversarial Nets  
- **저자**: Ian Goodfellow, Jean Pouget-Abadie, Mehdi Mirza, Bing Xu, David Warde-Farley, Sherjil Ozair, Aaron Courville, Yoshua Bengio  
- **링크**: [arXiv:1406.2661](https://arxiv.org/abs/1406.2661)  
- **발표년도**: 2014

본 논문은 **생성적 모델 학습을 위한 새로운 프레임워크**, Generative Adversarial Network (GAN)을 제안하였다. GAN은 두 개의 신경망, Generator(G)와 Discriminator(D)가 서로 경쟁하는 구조를 통해 고품질의 데이터를 생성하는 모델이다.

<br>

## 2. GAN 구조

```text
   [Noise z] ─────▶ Generator ─────▶ Fake Image ─────┐
                                                     ▼
                                                  Discriminator
   [Real Image] ────────────────────────────────────┘

                          ▲                        │
                          └─── Real or Fake? ◀─────┘
```

- **Noise z**: 임의로 샘플링된 잠재 벡터 (노이즈)
- **Generator (G)**: z로부터 가짜 이미지를 생성
- **Fake Image**: G가 생성한 이미지, D는 이를 진짜처럼 속이도록 G를 학습시킴
- **Real Image**: 실제 데이터셋에서 추출된 이미지
- **Discriminator (D)**: 입력이 실제 이미지인지, G가 만든 이미지인지 판별
- **Output**: D는 0~1 사이의 값을 출력 (1에 가까울수록 실제 이미지라고 판단)

<br>

## 3. 학습 목표

- **Generator (G)**는 Discriminator를 속이도록 학습
- **Discriminator (D)**는 실제와 가짜 이미지를 잘 구분하도록 학습

두 모델은 동시에 훈련되며, 게임 이론적 관점에서 **최적 균형 상태(Nash Equilibrium)**에 도달하면:

- G는 실제 데이터 분포와 동일한 이미지를 생성
- D는 진짜/가짜를 구별할 수 없으므로 항상 0.5의 확률을 출력

<br>

## 4. 수학적 정의

GAN의 목적 함수는 다음과 같다:

$$
\min_G \max_D V(D, G) = \mathbb{E}_{x \sim p_{\text{data}}(x)}[\log D(x)] + \mathbb{E}_{z \sim p_z(z)}[\log (1 - D(G(z)))]
$$

- G는 D를 최대한 속이도록 (loss를 최소화)
- D는 진짜와 가짜를 잘 구분하도록 (loss를 최대화)

<br>

## 5. 장점

- 복잡한 샘플링 과정 없이 고품질 데이터를 생성할 수 있음
- 잠재 변수 \( z \)에 대해 명시적 추론 과정이 필요 없음
- 단순한 backpropagation만으로 학습 가능
- Markov Chain 같은 추가적인 계산 필요 없음
- Generator는 Discriminator의 피드백만으로도 훈련 가능 (간접 학습)

<br>

## 6. 단점

- Generator가 학습한 분포의 **명시적 확률 표현이 불가능**
- 학습 안정성이 낮고, G와 D의 **학습 속도 균형**이 매우 중요함
- G가 너무 자주 업데이트되면 **모드 붕괴(mode collapse)** 발생 가능
- Discriminator가 너무 강해지면 Generator는 효과적인 학습을 하지 못함

<br>

## 7. 결론

GAN은 이후 다양한 생성 모델 (DCGAN, WGAN, CycleGAN 등)의 기초가 된 혁신적인 프레임워크이다. 단순한 아이디어(두 모델의 경쟁)를 통해 고차원의 복잡한 데이터 분포를 학습할 수 있도록 만든 이론적 기여는 매우 크다. 다만, 학습의 안정성과 평가 지표 등은 여전히 연구 중인 과제이다.

<br>

## 참고 문헌

- [arXiv:1406.2661](https://arxiv.org/abs/1406.2661)  
- Goodfellow, Ian, et al. "Generative adversarial nets." *Advances in neural information processing systems* 27 (2014).