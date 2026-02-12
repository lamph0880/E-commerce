사용한 변수 리스트
base: "ID", "Gender", "Customer_rating"를 제외한 모든 변수
      ['Customer_care_calls',
      'Cost_of_the_Product',
      'Prior_purchases',
      'Product_importance',
      'Discount_offered',
      'Weight_in_gms',
      'Reached.on.Time_Y.N',
      'Discount_Group',
      'Weight_Category',
      'Wh_Shipment_Combo',
      'Discounted_price',
      'Price_per_weight',
      'log_price_per_weight',
      'log_price_per_weight_sq',
      'Discount_rate',
      'Management_score',
      'Management_score_sq',
      'Warehouse_block_A',
      'Warehouse_block_B',
      'Warehouse_block_C',
      'Warehouse_block_D',
      'Warehouse_block_F',
      'Mode_of_Shipment_Flight',
      'Mode_of_Shipment_Road',
      'Mode_of_Shipment_Ship']

no warehouse: 위의 base에서 Warehouse_block 원-핫 인코딩 변수들을 제외한 변수
      ['Warehouse_block_A', 'Warehouse_block_B', 'Warehouse_block_C', 'Warehouse_block_D', 'Warehouse_block_F']를 제외한 변수

no shipment: 위의 no warehouse에서 추가로 Mode_of_Shipment 원-핫 인코딩 변수들을 제외한 변수
      ['Mode_of_Shipment_Flight', 'Mode_of_Shipment_Road', 'Mode_of_Shipment_Ship']를 제외한 변수
         -> 이 시점부터 데이터상 앙상블 점수의 상위권 등장

no weight: 위의 no shipment에서 추가로 Weight_in_gms 변수를 제외한 변수
      -> 앙상블 점수의 최고점 0.79

no management: 위의 no weight에서 추가로 Management_score를 제외한 변수

no importance: 위의 no management에서 추가로 Product_importance를 제외한 변수

no call: 위의 no importance에서 추가로 Customer_care_calls를 제외한 변수

no price per weight: 위의 no call에서 추가로 price per weight를 제외한 변수
      -> 앙상블 점수가 더이상 나타나지 않음

no cost: 위의 no price per weight에서 추가로 Cost_of_the_Product를 제외한 변수

no discount rate: 위의 no cost에서 추가로 Discount_rate를 제외한 변수

no discount offered: 위의 no discount rate에서 추가로 Discount_offered를 제외한 변수


[2026-02-11, 14:28]

variant_baseline_model_test
test_comparison
test_result
업로드
 variant_baseline_model_test로 생성한 모델의 결과를
test_result의 csv파일에 저장하였고
test_comparison에서 불러올수있음







[2026-02-09, 14:23] v3.1

1. 파생변수 추가
- 'log_price_per_weight'와 'Management_score'는 지연율을 설명할때 2차함수의 움직임을 따라가므로 polynomial을 판다스로 구현
'log_price_per_weight_sq'와 'Management_score_sq'를 생성.
원본과 제곱버전의 특성 모두 변수로 사용해야 한다고 함.

+ one-hot encoding 에서 'Wh_Shipment_Combo' 삭제(파생변수 생성시 레이블링 적용)

[2026-02-09, 11:40] v3.0

1. 특성간 분포 단순비교의 시각화 일부 수정
 - 무게, 할인, 가격의 kdeplot을 histplot으로 변경

2. 파생변수 추가
 - 'Wh_Shipment_Combo'의 설명 eda 파트에 추가
 - 'Management_score' 추가

3. 'log_value_density'의 이름을 'log_price_per_weight'로 변경

4. 전처리 함수, 인코딩, 스케일링 수정

hot fix
화물 관리 지표 'Management_score': '지표'를 '레벨'로 수정

추후 과제
변수에 따른 모델 성적 확인?
파이프 라인 구성?


[2026-02-09, 09:00] v2.2
1. EDA에 '데이터 비교분석 결과 해석' 섹션 추가
   - 그 하위 섹션에서 데이터 해석 및 파생변수 생성 과정 추가
 

2. 파생변수 추가 
설명과 전처리 함수, 스케일링을 위한 num_features 목록에 추가
  -'Discount_group', 'Weight_Category', 'Discounted_price', 'log_price_per_weight' 

추후 남은 진행과제:
'customer_care_calls' 세부 분석 및 정리
어떤 파생변수가 나은 성능을 보이는지 비교분석



[2026-02-06, 15:00] v2.1
1. 히트맵 아래 마크다운 셀 추가

2. 각 특성별 지연율
   - 가장 위의 셀에 주석 추가
   - 범주형과 숫자형 시각화 분석 마크다운셀 2개 수정

3. EDA
   - 첫줄에 마크다운 셀 추가
   - 특성간 분포 단순비교 시작 전, 'Prior_purchases'와 'Customer_care_calls'의 범주 다듬는 셀을 하나로 통합, 주석 추가 + 오류 해결
   - 가격,무게,할인액 비교 kdeplot에 산점도 추가
      ->kdeplot이 리소스를 많이 사용해서 산점도를 추가. 이후 kdeplot 제거할지 고민중. 피드백 요망

4.  중복 특성 정리 - 줄바꿈

5. 효율성을 위해 clear all output으로 저장 


추후 수정사항
특성별 인사이트와 가설 정리



[2026-02-06, 12:00]
1. 파일 이름 수정
기존의 것 -v1 
업데이트된 파일 -v2

2.함수 수정
위치: EDA - 특성간 비교함수 정의 - 수치형 특성과의 관계 분석 함수
summarize_numeric_with_plots 함수의 오류 수정

3. 가독성정리
기존 특성별 분포 비교의 기계적인 시각화부분을 '특성 간 분포 단순 비교'로 묶고 별도의 섹션으로 분리
평소에는 숨겨놓고 필요시에만 펼쳐볼수 있도록.

4. feature engineering 정리
함수 수정, 새로 추가된 파생변수 목록 정리
중복된 특성 목록 정리
스케일링 적용 특성 목록 정리 -> 기존에는 범주형 숫자도 스케일링 되었던 것을 확인, 이를 수정함


추후 수정사항
함수에서 futurewarning이 뜨는 것을 확인, 수정 필요
특성별 단순 비교 후 생기는 인사이트/가설을 정리