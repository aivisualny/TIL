# ARC Prize 2025 DSL + Postprocessing 기반 Solver (개선 전)

본 프로젝트는 **ARC Prize 2025** 대회를 위한 DSL 기반 ARC 문제 해결 시스템입니다.  

Primitive Rule 기반 DSL 시스템 + Beam Search 탐색 + ColorReplace 후처리 + 복합 후처리 조합을 구현합니다.

<br>

## 사전 설명

### 1. DSL?

    DSL은 Domain-Specific Language의 줄임말, 특정 분야에 특화된 프로그래밍 언어. 예를 들어, "상자에서 사과를 꺼내 바구니에 넣는다."의 python 표현은 if box.has('apple') ~ 이처럼 해야하지만, DSL에서는 move apple from box to basket 이렇게 직관적으로.

    한정된 작업을 자주 반복해야 하는 상황에서 빠르고 정확하게 작업을 도움.

### 2. Beam Search?

    너무 많은 경우의 수를 다 살펴보지 않고, 가장 유망한 몇 개만 계속 따라가면서 답을 찾는 방법.

    각 단계에서 하나의 규칙을 적용했을 때, 다음 상태로 진행할 수 있는 후보가 여러 개 생기는데, 출력과 비슷한 후보들만 유지.

### 3. 후처리?

    예측 결과가 거의 맞지만, 아주 약간 수정만 하면 정답이 되는 경우, 예를 들어 정답과 형태는 같은데 색만 다를 경우. 조건에 맞춰 색만 바꿔주면 된다.

    마지막에 정답에 더 가깝게 보정하는 과정.

##  전체 파이프라인 구조 요약

```
1. 데이터 로드
   ↓
2. Primitive Rule 정의 및 확장
   ↓
3. Rule 매칭 평가 (evaluate_rules)
   ↓
4. Beam Search 기반 DSL 시퀀스 탐색
   ↓
5. 탐색된 DSL 시퀀스 분포 시각화
   ↓
6. 후처리 조합 함수 자동 등록
   ↓
7. ColorReplace 및 후처리 조합 적용
   ↓
8. 최종 성능 평가 및 시각화
   ↓
9. unknown 사례 저장
   ↓
10. 제출 JSON 생성
```

<br>

##  Cell별 상세 설명

<br>

###  **Cell 1: 기본 모듈 불러오기 및 데이터 로드**

- 외부 JSON 데이터(`arc-agi_training_challenges.json`, `arc-agi_training_solutions.json`)를 불러와 학습용 데이터 구성
- 시각화를 위한 컬러맵 정의
- 출력 포맷: `{'input': grid, 'output': grid, 'task_id': str, 'id': str}`

<br>

###  **Cell 2: Primitive Rule 클래스 정의**

- `BaseRule`: 규칙 클래스의 추상 부모 클래스
- 다양한 기본 규칙들 정의:
  - 대칭, 회전, transpose 등 2D 행렬 기반 연산
  - 객체 추출 및 위치 이동 (e.g., `extract_largest_object`, `place_top_left`)
  - `color_replace`는 match만 담당, apply는 후처리로 분리됨
- 이 구조는 rule-based generalization을 위한 DSL의 기반이 됨

<br>

###  **Cell 3: 사용할 규칙 리스트 정의**

- 탐색 대상 DSL 규칙들을 `rule_list`로 구성
- `ColorReplaceRule`은 후처리 전용이므로 제외

<br>

###  **Cell 4: 문제별 규칙 적용 결과 평가**

- 각 문제(`input` → `output`)에 대해 DSL 규칙이 적용 가능한지 평가
- 적용 가능한 rule 목록이 없으면 `["unknown"]`으로 분류
- 결과: `rule_results` 리스트 생성

<br>

###  **Cell 5: DSL 탐색기 (Beam Search 기반)**

- Beam Search를 활용해 DSL 규칙 조합 탐색
- 탐색 파라미터: `max_depth=3`, `beam_width=5`
- 최소 오류(`grid != target`)를 기준으로 우선순위 큐에서 후보 유지
- 목표는 output과 정확히 일치하는 DSL 시퀀스를 찾는 것

<br>

###  **Cell 6: Beam Search 평가 함수**

- 위 Beam Search를 각 데이터에 실행
- 출력: `beam_results` (id, task_id, matching_rules 포함)

<br>

###  **Cell 7: beam_results 시각화**

- `beam_results`의 rule sequence를 `_`로 연결하여 카운팅
- Top 15 rule 시퀀스 빈도 분포를 bar chart로 시각화

<br>

###  **Cell 8: 후처리 연산자 조합 자동 등록**

- 후처리의 핵심 목표: shape mismatch 또는 color mismatch 해결
- ColorReplace 기준의 동치 여부(`is_color_replace_only`) 확인
- 다양한 연산자 조합 생성 (단일, 2단계, 3단계)
- 추가된 후처리 조합 예시:
  - `pad_to_shape_then_color_replace`
  - `rotate_then_crop_center_then_color_replace`
- `POSTPROCESS_FUNCTIONS` 리스트에 자동 등록

<br>

###  **Cell 9: ColorReplace 후처리 적용 통합**

- `beam_results`의 DSL 시퀀스를 실제로 apply
- 이후 정답과 shape/color가 안 맞는 경우, 다양한 후처리 조합을 적용
- 최종적으로 가장 잘 맞는 `pred_grid`와 함께 `postprocessed_results` 생성
- 후처리 전략:
  - 단순 `color_replace`
  - 복합 후처리 (e.g., transpose → rotate → color_replace)
  - shape mismatch padding

<br>

###  **Cell 10: 후처리 결과 시각화**

- 후처리까지 적용된 최종 DSL 시퀀스 분포 시각화
- 목적: 어떤 조합이 자주 쓰였는지 확인 가능

<br>

###  **Cell 11: unknown 예제 저장 및 성능 평가**

- 여전히 해결되지 않은 예제(`['unknown']`)를 추출하여 JSON 저장
- 최종 정확도 평가:
  - `np.array_equal(pred, target)` 기준
  - 콘솔 출력: 정확히 맞춘 개수 및 정확도 비율

- 제출용 JSON(`submission.json`) 작성:
  ```json
  {
    "task_id_1": [[...], [...]],
    "task_id_2": [[...], [...]],
    ...
  }
  ```

<br>

## 핵심 특징 요약

- **DSL 기반 탐색**: 사전에 정의된 rule 조합으로 범용적 문제 해결 가능
- **Beam Search**: 복잡한 조합을 제한된 탐색 폭 내에서 효율적으로 탐색
- **후처리 확장성**: ColorReplace 기반 후처리 자동 탐색 및 조합
- **Generalization**: 단순 규칙만으로도 다양한 ARC 문제를 커버하려는 시도