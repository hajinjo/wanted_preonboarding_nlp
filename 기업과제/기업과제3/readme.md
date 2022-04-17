# STS 문장유사도 모델 

**기업과제3_7팀.ipynb** : 모델 만들고 튜닝한 파일 

**기업과제3_7팀(score).ipynb** : 만든 모델로 f1 score 계산해 dev_set.csv 만드는 파일 

-------------

### 사용한 모델 
한국어로 사전훈련된 klue/roberta-base 모델을 사용해 fine-tuning 

### 데이터  

Klue STS(airbnb reviews, policy news, paraKQC) 

한글로 된 두개의 문장 쌍 데이터

## 훈련과정 

1. 데이터 전처리 (token화 등)
2. metric으로 f1 score와 pearson 사용하여 학습 

3. Optuna package 이용하여 하이퍼 파라미터 서치
   - epoch : earlystopping으로 score 변화 없을시 종료되게 수행
   - random seed : grid search로 탐색해 좋은 값을 내주는 seed를 얻음
   - 학습률은 스케쥴러를 사용하는 것과 고정값을 주는 2가지 방식으로 실험 => 스케쥴러 사용한 것이 더 높은 스코어  
   - 배치사이즈는 작은 편이, weight_decay는 넣는 것이 스코어가 더 높았음 

   - 여러가지 실험을 거쳐 세개의 모델로 후보군을 좁히고 F1과 피어슨이 골고루 높은 모델을 최종 모델로 선정 
![스크린샷 2022-04-17 오후 1 52 16](https://user-images.githubusercontent.com/83392231/163700979-d2cb92a3-cbf2-4034-949a-6f12d68b1818.png)

4. Evaluate & Predict 

   - test set으로 평가 및 예측 진행 
  
5. 최종모델로 fastAPI 제작 


### 결과
>Baseline : F1 score 0.854, Pearson 0.925
>모델 : F1 score 0.87, Pearson 0.88

### 보완할점
- 단어가 비슷하면 긍정/부정의 뜻이 달라도 비슷한 문장이라고 인식
- 단순히 문장 자체를 토큰화 해서 비교하는 방식이어서 발생하는 문제로 보인다 

- 데이터 셋을 더 늘리거나 노이즈를 추가하여 학습해서 모델에 다양성 주기 
- 유사도 뿐만 아니라 다양한 metric을 이용해서 semantic 적인 유사도도 파악 할 수 있는 모델로의 발전 

[로컬호스트를 통해 결과를 확인 할 수 있는 Api 코드](https://github.com/hajinjo/pre_onboarding_assignment_FAST_API)

실행 스크린샷 
![127 0 0 1_5000_](https://user-images.githubusercontent.com/83392231/161547179-dd1df4b0-a34e-4dfe-89e4-6fca61a8be73.png)

![127 0 0 1_5000_result](https://user-images.githubusercontent.com/83392231/161547187-280c8bca-9966-479e-b60d-ea57aa834b19.png)
