# E-Commerce Shipping Data
전자상거래 데이터를 분석을 통해 배송 지연을 사전에 파악하고, 고객 만족도를 높이기 위한 서비스 모델을 만들고자 합니다.

실제로 서비스를 할 수 있는 모델을 목적으로 진행했습니다.
단순히 지연이 됐다, 안됐다를 맞추는 Accuracy를 높이는 것이 아니라, 지연이 될 화물을 정확히 예측하는 것이 목표입니다.
그래서 평가 지표 중 Recall과 F1_Score, AUC_Gap을 중요하게 생각했습니다.

Accuracy : '지연'과 '정상'을 모두 맞춘 비율
Precision : '지연'이라고 예측한 것 중 실제 '지연'인 비율
Recall : 실제 '지연'인 것 중 '지연'이라고 예측한 비율
F1_Score : Recall과 Precision의 조화평균
AUC_Gap : 모델의 과적합 방지

### 1. 조원 및 역할분담
정영석 (조장) : 데이터 분석 및 피쳐 엔지니어링   
권희민 : 모델링 및 하이퍼파라미터 진행   
김보미 : 모델 평가 및 서비스 기획   
김대원 : 코드 통합 및 프로젝트 발표   
   
### 2. 사용한 기술 스택   
* 데이터 분석 : pandas, numpy, matplotlib, seaborn, sklearn, math
* 모델링 : LogisticRegression, KNeighborsClassifier, SVC, DecisionTreeClassifier, RandomForestClassifier, GradientBoostingClassifier, ExtraTreesClassifier, StackingClassifier, XGBClassifier, LGBMClassifier, CatBoostClassifier, Pipeline, randit, uniform, AutoGluon
* 평가 지표 : sklearn.metrics

### 3. 데이터 분석 및 모델링 과정
데이터의 특성 간 상관관계를 시각화해서 데이터를 분석, 지연에 영향을 미치는 특성을 파악합니다.
   * Discount_offered   
           이유 : 일정 금액 이상의 할인을 받으면 100% 지연되지 않는 특수한 패턴이 있습니다.    
           특성 중 가장 타겟 특성과의 관계가 가장 강한 특성입니다.
    
   * Weight_in_gms   
              이유 : 특정 무게 구간에서 지연이 빈번하게 발생합니다.   
              특성 중 지연율에 어느 정도 영향을 끼치는 특성입니다.

   * Cost_of_the_Product
              이유 : 해당 특성만 사용할 때에는 큰 영향이 없지만
              다른 컬럼과 함께 사용하면 지연 예측에 영향을 끼치는 특성입니다.

피쳐 엔지니어링을 통해 모델 성능 향상   
   * 파생 변수 생성    
              이유 : 부족한 데이터를 보완하기 위해 파생 변수를 생성합니다.   
          Discount_Group : 할인율이 10 이상인 경우
          Weight_Category : 무게 구간별 지역특성 그룹화
          Discounted_price : 할인된 가격
          Discount_rate : 가격 대비 할인 비중
          log_price_per_weight : 무게 대비 가격 (로그 변환)  

모델링 및 하이퍼파라미터 튜닝을 통해 모델 성능 최적화
   * Gap 설정
        데이터 특성 상 모델의 과적합을 방지하기 위해 설정

   * Pipeline
        데이터 누수 방지 및 전처리 과정을 파이프라인으로 구성

   * 베이스라인, 파생변수추가, 중복변수제거 등 여러 방식으로 모델링
        여러 모델을 기본 파라미터로 학습
        과적합이 아닌 모델 중 성능이 좋은 모델을 선택

        중복 변수 제거, 변수 갯수 줄이기를 한 모델 중 GradBoost 모델이 가장 좋은 성능을 보임
        Accuracy : 0.679
        Precision : 0.873
        Recall : 0.541
        F1_Score : 0.668
        AUC_Gap : 0.082
     
   * 하이퍼파라미터 튜닝
        RandomdizedSearchCV와 GridSearchCV를 사용하여 최적의 파라미터를 찾고,
        튜닝 전과 튜닝 후 모델을 비교하여 성능이 가장 좋은 모델을 선택
     
        RandomizedSearchCV
        Accuracy : 0.690
        Precision : 0.943
        Recall : 0.511
        F1_Score : 0.663
        AUC_Gap : 0.013

        GridSearchCV
        Accuracy : 0.689
        Precision : 0.949
        Recall : 0.506
        F1_Score : 0.660
        AUC_Gap : 0.009

   * 실제 테스트 데이터로 모델 성능 평가
        GridSearchCV로 튜닝한 모델이
        AUC_Gap : 0.009로 가장 좋은 성능을 보임

   * 수동모델과 자동모델을 비교하여 최적의 모델을 선택
        AutoGluon Medium, High, Best 모델과 비교
        Grid -> Best 순으로 성능이 좋았음

### 4. 모델 평가
최종 결정된 Grid 모델의 성능을 평가
     장점
     - 모델의 정밀도(Precision)의 수치가 높기 떄문에 예측한 '지연'은 실제로 '지연'일 확률이 높습니다.
      
     단점
     - 모델의 재현율(Recall)의 수치가 낮아 전체 '지연' 중 예측하지 못하는 경우가 많습니다. 

     개선점
     - 데이터의 특성이 부족해 정확도 및 재현율을 높이는 데 한계가 있었습니다.
     - 더 많은 데이터를 확보하여 모델의 성능을 개선할 수 있습니다.

### 5. 서비스 기획
현재 모델의 성능을 바탕으로 서비스를 기획합니다.
     무릎점을 운영임계점으로 설정, 40%부터는 정밀도가 급격히 하락하기 때문에 최대를 40%로 설정

     1. 효율 중시
     임계점 : 0.7858
     운영 비중 : 29%
     정밀도 : 0.96
     특징 : 지연이라고 예측한 것은 실제로 거의 지연이 되기 때문에, 비용을 최소화 할 수 있다.

     2. 리스크 관리
     임계점 : 0.7804
     운영 비중 : 40%
     정밀도 : 0.84
     특징 : 정밀도는 떨어져 비용은 더 들어가지만, 더 많은 지연을 예측할 수 있다.
