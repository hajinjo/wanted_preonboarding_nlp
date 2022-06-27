# STS 문장유사도 모델 

**기업과제3_7팀.ipynb** : 모델 만들고 튜닝한 파일 

**기업과제3_7팀(score).ipynb** : 만든 모델로 f1 score 계산해 dev_set.csv 만드는 파일 

-------------
## ✔️ 개요

### 주제

- 문장 pair의 유사도를 분석하는 모델 제작 팀프로젝트(5명)
- 해당 프로젝트는 원티드 프리온보딩에서 진행한 기업과제

### 데이터

- **KLUE/STS 데이터** : 한국어 문장 유사도를 비교하기 위해 Airbnb, PARICY, PARAKQC 세 종류의 데이터로 구성한 데이터셋
    - Policy News (POLICY) : 대한민국 부처, 국가청, 국가위원회가 배포하는 다양한 문서 데이터
    - ParAKQC(PARAKQC) **:** 스마트홈 기기를 겨냥한 10,000개의 발화 데이터
    - Airbnb Reviews (AIRBNB) **:** AIRBNB 홈페이지에서 공개적으로 접속할 수 있는 리뷰 데이터

![KLUE 공식 벤치마크 사이트](https://user-images.githubusercontent.com/83392231/175848189-cb75e471-8258-4b5e-bdf2-59a72c0efc88.png)

KLUE 공식 벤치마크 사이트

- 두 입력 문장의 의미적 유사성을 0에서 5까지 라벨링, 모델 성능은 Pearson의 상관 계수로 측정
- 실수를 임계값 점수가 3.0인 두 개의 클래스로 이진화하고 F1 점수를 사용하여 모델을 평가

### 모델 선택

- 한국어로 사전훈련된 KLUE/RoBERTa-base 사용
- 기존 BERT보다 더많은 데이터를 사용, Dynamic Masking를 수행하여 문장 유사성 부분에서 SOTA을 도달한 모델이기 때문에 RoBERTa 모델의 한국어 버전인 KLUE/RoBERTa 선택

### 담당한 역할

- 모델 학습 및 파라미터 튜닝, 논문 서치
    - reference : [klue paper](https://arxiv.org/pdf/2105.09680.pdf), [adamW](https://arxiv.org/pdf/1711.05101.pdf)

## ✔️ 프로젝트 진행

### 모델 학습

- Tokenizer를 사용하여 512의 길이로 데이터셋 안의 sentence1,2를 전처리
- 허깅페이스의 Trainer 사용
- metric은 f1 score가 최대가 되도록, loss는 adamW를 사용해 학습

### 하이퍼 파라미터 튜닝

![Test 실행 시 F1 : 0.82 , Pearsonr : 0.84 (https://user-images.githubusercontent.com/83392231/175848314-a817ae54-9154-4434-bd4b-a56519391721.png)

Test 실행 시 F1 : 0.82 , Pearsonr : 0.84

- **Epoch** : earlystopping으로 score 변화 없을시 종료되게 수행
- **Seed** : random seed 마다 score의 차이가 있기 때문에 제일 먼저 seed를 탐색하여 가장 높은 점수가 나온 seed = 42로 고정
- **Learning rate** : 스케쥴러를 사용하는 것과 고정값을 주는 두가지 방식으로 실험
- batch_size [16,32,64]로 테스트
- weight decay를 추가하여 성능 변화 확인
- Optuna를 통해 랜덤으로 best 파라미터 search 하는 방식도 추가

### 튜닝 결과

![3개의 모델의 결과값 확인](https://user-images.githubusercontent.com/83392231/175848321-3c5ed708-a84f-46b3-a628-b6cc00252e9d.png)

3개의 모델의 결과값 확인

- LR 스케쥴러를 사용한 것이 고정값을 주는 것보다 전반적으로 더 높은 스코어가 나옴
- 배치 사이즈는 작은 편이, weight_decay는 넣는 편이 스코어가 더 높았음
- 실험결과 짧은 문장 두 쌍을 비교하는 보다 단순한 task 특성상, 비교적 적은 데이터와 적은 에포크 수로도 금방 최적점을 찾아갔으며, 작은 배치사이즈를 사용해도 학습시간이 그렇게 오래 걸리지 않고 수렴이 잘 되었던 것으로 보임

여러 방식으로 파라미터 튜닝 실험을 거친 결과 optuna로 best parameter search를 하는 방식이 F1과 피어슨 점수가 골고루 높아서 최종 모델로 선정 

### API 제작

만든 모델을 이용해 두 개의 문장 입력시 유사도를 판별해주는 API 제작


![스크린샷 2022-06-19 오후 2 14 07](https://user-images.githubusercontent.com/83392231/175848388-9523f01f-5e2e-480b-b9dc-06927e86f012.png)

![스크린샷 2022-06-19 오후 2 14 19](https://user-images.githubusercontent.com/83392231/175848392-4c149763-0c87-4e3e-87e9-016dd0300e66.png)

![스크린샷 2022-06-19 오후 2 14 39](https://user-images.githubusercontent.com/83392231/175848396-6cdc4368-449c-46fe-9f58-ae7146937b82.png)

> 문장1 :최근 국민들의 여행심리 위축 등으로 동남아 등 다른 노선까지 영향을 받는 상황이다.
문장2 :동남아시아와 같은 다른 노선은 최근 사람들의 여행 감정의 하락에 영향을 받았습니다.
> 

테스트 문장 입력시 마지막 이미지의 유사도 출력 

## ✔️결과

![파란색 선 : 베이스라인, 주황색 선 : 최종 모델 ](https://user-images.githubusercontent.com/83392231/175848427-b70da2b8-8d4f-4cfd-b0a7-53ed32c549c9.png)


파란색 선 : 베이스라인, 주황색 선 : 최종 모델 

> Baseline F1 score : 0.82   Pearson : 0.85
Model     F1 score : 0.87   Perason : 0.88
> 

### 활용

- 논문 등 문서의 표절 탐지
- 비슷한 내용의 책, 가사 등 검색&추천 시스템에 활용 가능
- 유사도를 활용해 문서 분류, 토픽 모델링, 중요 문장 추출(요약) 등 다양한 모델로의 확장

### 개선방향

- 단순히 문장 자체를 토큰화 해서 비교하는 방식이어서 겹치는 단어가 많으면 긍정/부정의 뜻이 달라도 유사도가 높다고 나온다.
    - 이러한 한계점을 개선하기 위해서 유사도 뿐만 아니라 감성분석 기법도 섞어서 semantic적인 유사도도 파악 할 수 있는 모델로 발전시킬 수 있을 것이다.
- 데이터 셋을 늘리거나 노이즈를 추가하여 모델에 다양성 주기. 추가 데이터를 사용하지 말라는 룰이 있어서 한정된 데이터만 사용했는데 더 많은 문장 쌍을 포함시키면 더욱 성능이 올라갈 것으로 예상한다.
