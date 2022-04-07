# STS 문장유사도 모델 

**기업과제3_7팀.ipynb** : 모델 만들고 튜닝한 파일 

**기업과제3_7팀(score).ipynb** : 만든 모델로 f1 score 계산해 dev_set.csv 만드는 파일 

-------------

klue 데이터는 한글로 된 두개의 문장 쌍 데이터로 F1 score와 피어슨 상관계수로 평가 

한국어로 사전학습된 klue/roberta-base 모델을 사용해 fine-tuning

### 결과
>pearson score : 0.88  
>f1 score: 0.87

자세한 과정은 [pdf 파일](https://github.com/hajinjo/wanted_preonboarding_nlp/blob/main/%EA%B8%B0%EC%97%85%EA%B3%BC%EC%A0%9C/%EA%B8%B0%EC%97%85%EA%B3%BC%EC%A0%9C3/%E1%84%80%E1%85%B5%E1%84%8B%E1%85%A5%E1%86%B8%E1%84%80%E1%85%AA%E1%84%8C%E1%85%A63_%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%E1%84%87%E1%85%A9%E1%84%80%E1%85%A9%E1%84%89%E1%85%A5.pdf)로 확인 가능합니다. 

[로컬호스트를 통해 결과를 확인 할 수 있는 Api 코드](https://github.com/hajinjo/pre_onboarding_assignment_FAST_API)

실행 스크린샷 
![127 0 0 1_5000_](https://user-images.githubusercontent.com/83392231/161547179-dd1df4b0-a34e-4dfe-89e4-6fca61a8be73.png)

![127 0 0 1_5000_result](https://user-images.githubusercontent.com/83392231/161547187-280c8bca-9966-479e-b60d-ea57aa834b19.png)
