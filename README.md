# 고객 행동 데이터을 사용한 회원 탈퇴 예측 

### 분석 결과 및 사각화
1. 웹 주문수 분석
- 결론: 매출이 가장 높은 달은 7월, 가장 낮은 달은 5월임을 확인할 수 있으며, PC-E의 매출이 다른 상품에 비해 압도적으로 높다.
<img width="497" alt="Screenshot 2024-04-15 at 1 51 52 PM" src="https://github.com/jiapn123/marketing-data-analysis/assets/155503641/1d44af0a-75fa-40a6-ac91-e97d2d0ea140">

2. 대리점 데이터 가공
- item_name에 공백이 포함되거나 대문자, 소문자가 섞여있는 오류 수정 （'상품S'와 '상품s'）
- 금액의 결측치 상품명이 같은 제품의 price를 가져와 결측치를 수정함 
- 고객 이름 오류 수정 (김 현상 -> 김현상)
- 날짜 오류 수정(2018-01-04 00:00:00 -> 2018-01-04 / 42782 -> 2017-02-18)

3. 고객의 전체 모습을 파악
- 결론1: 회원 기간이 10개월 이내인 고객이 많고, 10개월 이상의 고객 수는 거의 일정 
<img width="515" alt="Screenshot 2024-04-15 at 3 29 43 PM" src="https://github.com/jiapn123/marketing-data-analysis/assets/155503641/1277206b-0350-40e2-949f-599b943aa02f">

- 결론2: 평균, 중앙값, 최댓값, 최솟값 모두 지속 회원보다 탈퇴 회원이 작다. 지속 회원은 0.98로 정기적으로 이용하는 것을 알 수 있지만, 탈퇴 회원은 0.45로 절반정도는 랜덤하게 이용하고 있다.
<img width="903" alt="Screenshot 2024-04-15 at 3 32 29 PM" src="https://github.com/jiapn123/marketing-data-analysis/assets/155503641/89344371-719c-494a-b1a1-892520011e45">

4. 고객의 행동 예측
- 고객의 모습 클러스터링 결과 시각화(그룹0, 1이 지속회원이 많음, 그룹2는 탈퇴회원만 있음, 그룹3은 골고루 포함되어 있음)
<img width="480" alt="Screenshot 2024-04-15 at 3 38 56 PM" src="https://github.com/jiapn123/marketing-data-analysis/assets/155503641/a10859cb-2c08-4902-bcce-d5ccce84a7dd">

- 다음달 이용횟수를 예측하는 모델 구축
```python
from sklearn import linear_model
import sklearn.model_selection

model = linear_model.LinearRegression()

predict_data = predict_data.loc[predict_data["start_date"]>=pd.to_datetime("20180401")]
X = predict_data[["count_0","count_1","count_2","count_3","count_4","count_5","period"]]
y = predict_data["count_pred"]
X_train, X_test, y_train, y_test = sklearn.model_selection.train_test_split(X,y)
model.fit(X_train, y_train)
```
- x1, x2 회원 다음달의 이용 횟수 예측
``` python
x1 = [3, 4, 4, 6, 8, 7, 8]
x2 = [2, 2, 3, 3, 4, 6, 8]
x_pred = [x1, x2]

model.predict(x_pred)
```
예측 결과: array([3.76788889, 2.02178817])
