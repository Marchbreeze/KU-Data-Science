## 1. 회귀 평가

### (1) 결정계수

- $R^2$
    - 결정계수 : 회귀 식이 주어진 데이터에 꼭 맞는 정도 (0~1)
        
        ![2024-04-21_03-18-32.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/edfd69d1-6c01-4d0c-9269-1bae8a4e3915/2fe5f5ae-784e-4354-b529-5a423bf4a705/2024-04-21_03-18-32.jpg)
        
        - 잔차의 제곱합을 평균과의 차이의 제곱의 합으로 나눈 값
    - 종속 변수(y)의 변동을 설명할 수 있는 정도
    - 독립 변수(x)를 더 다양하게 투입할수록 값이 커지는 경향

- adjusted $R^2$
    - 수정된 결정계수
        
        ![2024-04-01_15-13-47.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/edfd69d1-6c01-4d0c-9269-1bae8a4e3915/77b54ebd-b5b7-4fb1-bb73-9a33a0656d9c/2024-04-01_15-13-47.jpg)
        
    - 독립 변수의 개수(p)가 많아져도, 무작정 증가하지 X
    
- 성능 계산 라이브러리 : `r2_score`
    
    ```python
    # 결정계수
    from sklearn.metrics import r2_score
    
    # 회귀 식을 적용했을 때의 결정계수 계산
    r2_score(y_valid, y_pred)
    ```
    
    ![2024-04-01_15-28-34.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/edfd69d1-6c01-4d0c-9269-1bae8a4e3915/2176835f-d34a-4786-a6f4-eec04367d4d5/2024-04-01_15-28-34.jpg)
    

### (2) 제곱근 오차

- `MSE` : 평균제곱오차
    
    ```python
    # 평균제곱오차
    from sklearn.metrics import mean_squared_error
    
    mean_squared_error(y_valid, y_pred)
    ```
    
- `RMSE` : 평균제곱근오차 = $\sqrt {MSE}$
    - 오차의 기존 scale를 따라가서 MSE보다 더 자주 사용됨
    - but, 두 RMSE를 비교해서 우수성을 비교하기엔 기존 scale에 영향

- `MAPE` : 평균절대비율오차(%)
    - 실제 값 대비 오차의 `비율`들을 계산하여 평균을 낸 값
    - ex)
        - 학생 1: 예측 200, 실제 250 → 50/250*100 = 20%
        - … 모든 학생의 %값을 구한 후 평균하기
    
- ex) 문제1
    
    ![2024-04-21_03-32-01.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/edfd69d1-6c01-4d0c-9269-1bae8a4e3915/4b8a7d40-1aad-4a85-b725-4e86c2efee5f/2024-04-21_03-32-01.jpg)
    
    - RMSE = 200
    - 결정계수 0.5 = 1 - (0,5 * N-1) / N -10 → 연립방적식에서 N = 55

---

## 2.  정규화

### (1) 과적합 문제

- `과적합`
    - 훈련 데이터에 과하게 맞추어 적합시키는 결과, 현실 데이터와 괴리감이 발생
        
        ![2024-04-01_15-50-13.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/edfd69d1-6c01-4d0c-9269-1bae8a4e3915/7f3549a9-1fc2-4dda-b20c-0df4240ddbf0/2024-04-01_15-50-13.jpg)
        
        - 전부 구별하기 위해 휘어있는 울타리 만들 수는 없음

- 과적합 해결 방법
    1. `feature selection`
        - 회귀식에서 불필요한 `독립 변수 제거`
    2. `regularization` (정규화)
        - 회귀식에서 `계수`의 크기(scale)를 줄여가며 독립 변수의 영향력을 낮춤

### (2) 정규화

- 목표 : `MSE 최소화` (for 성능) & `계수 최소화` (for 과적합 방지)
- 최소화할 정보 :
    
    ![2024-04-01_16-11-03.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/edfd69d1-6c01-4d0c-9269-1bae8a4e3915/df168e7d-158a-4836-aae2-d157772e96db/2024-04-01_16-11-03.jpg)
    
    - 선형 회귀의 손실 함수에 정규화 항을 추가하여 사용
    - alpha 값에 따라 성능, 과적합 문제의 중요성 조정 (기본값 : 1)

- 회귀 방식의 차이
    1. `Lasso 회귀`
        - L1 정규화 (norm 1)를 사용
        - 회귀 계수의 절대값의 합에 대한 패널티를 부여해 일부 회귀 계수를 정확히 0으로 만들어버림
        - 모델에서 불필요한 변수를 제거 → `feature selection`
    2. `Ridge 회귀`
        - L2 정규화 (norm 2)를 사용
        - 회귀 계수의 제곱의 합에 대한 패널티를 부여하여 모든 계수를 작게 만들어 과적합을 방지
        - 회귀 계수를 완전히 0으로 만들지는 않음

- 코드 구현 :
    
    ```python
    # 라쏘 정규화
    from sklearn.linear_model import Lasso
    
    reg_Lasso = Lasso(alpha = 0.5) 
    reg_Lasso.fit(x_train, y_train) 
    y_pred_Lasso = reg_Lasso.predict(x_valid)
    
    print(reg_Lasso.coef_)
    print(reg_Lasso.intercept_)
    ```
    
    ![2024-04-01_16-33-00.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/edfd69d1-6c01-4d0c-9269-1bae8a4e3915/0110732f-5201-4c9a-957e-ad60915bc351/2024-04-01_16-33-00.jpg)
    
    ```python
    # 릿지 정규화
    from sklearn.linear_model import Ridge 
    
    reg_Ridge = Ridge(alpha = 0.5) 
    reg_Ridge.fit(x_train, y_train) 
    y_pred_Ridge = reg_Ridge.predict(x_valid)
    
    print(reg_Ridge.coef_)
    print(reg_Ridge.intercept_)
    ```
    
    ![2024-04-01_16-33-22.jpg](https://prod-files-secure.s3.us-west-2.amazonaws.com/edfd69d1-6c01-4d0c-9269-1bae8a4e3915/c822a996-4b56-4687-90bd-6ed6f5979abe/2024-04-01_16-33-22.jpg)
