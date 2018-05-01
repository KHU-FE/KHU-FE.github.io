---
bg: "smartbeta.jpg"
layout: post
title:  "[Smart Beta] Chapter 2. 팩터의 탄생 : CAPM과 3팩터 모델 (2)"
crawlertitle: "[Smart Beta] CAPM & 3-Factor model"
summary: "자본시장선(CML, Capital Market Line)과 3-Factor model"
date:   2018-03-24 20:09:47 +0700
categories: posts
tags: ['Smart-Beta']
author: KHU-FE
---

# 팩터의 탄생 : CAPM과 3팩터 모델

# [그림 2.3] 미국 주식 시장 내 SMB와 HML 누적 수익률(1926~2016)

데이터 : 미국 시장 3팩터 일일 수익률 데이터 (1926~2016)
(출처:Kenneth R.French-Data Library)


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

data1 = pd.read_excel('F-F_Research_Data_Factors_daily.xlsx',index_col=0)

data1.index=pd.to_datetime(data1.index, format='%Y%m%d' )

SMB = np.cumprod(data1['SMB']/100+1)
HML = np.cumprod(data1['HML']/100+1)

fig, ax1 = plt.subplots()

ax2 = ax1.twinx()
ax1.plot(data1.index, SMB, 'g-')
ax2.plot(data1.index, HML, 'r-')

ax1.set_xlabel('Date')
ax1.set_ylabel('SMB', color='g')
ax2.set_ylabel('HML', color='r')
plt.show()

```


[![png]({{ site.images | relative_url }}/output_1_0.png)]({{ site.images | relative_url }}/output_1_0.png)



3팩터 모델은 CAPM의 시장베타로도 설명되지 않는 수익률의 잔여부분을 설명

* SMB(초록색): 대형주 대비 소형주의 초과 수익률
* HML(빨간색): 성장주 대비 가치주의 초과 수익률

두 팩터 모두 장기적으로 상승하는 모습을 보임
즉, 소형주일수록, 가치주일수록 수익률이 높으며 개별주식을 설명하는 새로운 팩터임을 확인

# [표 2.2] 삼성전자와 KOSPI 수익률 회귀분석 결과: CAPM 및 파마-프렌치 모형

데이터: 삼성전자, KOSPI 일일 데이터(2000~2016) 

#### * CAPM 회귀분석


```python
from scipy import stats

data2 = pd.read_excel('ch2_data.xlsx',index_col=0)

Mkt = data2['Rm']-data2['Rf']
data2.insert(loc=0, column = 'Mkt', value = Mkt)

x = [Mkt]
y = data2['Ri']-data2['Rf']

slope, intercept, r_value, p_value, std_err = stats.linregress(x,y)

print("Beta:", slope)
print("Alpha:", intercept)
print("r-squared:", r_value**2)
```

    Beta: 1.087772456834286
    Alpha: 0.003755726346532623
    r-squared: 0.6980514755453017
    
    

#### * 3팩터모델 회귀분석


```python
import statsmodels.api as sm

Y = y
X = data2[['Mkt','SMB','HML']]
X = sm.add_constant(X)

model = sm.OLS(Y,X)
results = model.fit()

#print(results.summary())
print('Alpha/Beta(M,S,H):', results.params)
print('R-squared:',results.rsquared)
```

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                      y   R-squared:                       0.722
    Model:                            OLS   Adj. R-squared:                  0.722
    Method:                 Least Squares   F-statistic:                     3641.
    Date:                Mon, 19 Mar 2018   Prob (F-statistic):               0.00
    Time:                        18:37:38   Log-Likelihood:                 11733.
    No. Observations:                4202   AIC:                        -2.346e+04
    Df Residuals:                    4198   BIC:                        -2.343e+04
    Df Model:                           3                                         
    Covariance Type:            nonrobust                                         
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    const          0.0008      0.000      1.733      0.083      -0.000       0.002
    Mkt            1.0005      0.012     86.632      0.000       0.978       1.023
    SMB           -0.3107      0.022    -14.009      0.000      -0.354      -0.267
    HML           -0.4669      0.030    -15.347      0.000      -0.527      -0.407
    ==============================================================================
    Omnibus:                      518.117   Durbin-Watson:                   1.782
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):             2412.412
    Skew:                           0.513   Prob(JB):                         0.00
    Kurtosis:                       6.567   Cond. No.                         136.
    ==============================================================================
    

    Alpha/Beta(M,S,H): const    0.000841
    Mkt      1.000455
    SMB     -0.310717
    HML     -0.466917
    dtype: float64
    R-squared: 0.7223491032054581
    

#### * 결론


```python
R = np.array([intercept,slope,0,0,r_value**2])
r = np.array([results.params[0],results.params[1],results.params[2],results.params[3],results.rsquared])

df = pd.DataFrame(data=[R,r], index =['CAPM','3Factor'], columns = ['Alpha','Mkt','SMB','HML','R-squared'])
print(df)
```

                Alpha       Mkt       SMB       HML  R-squared
    CAPM     0.003756  1.087772  0.000000  0.000000   0.698051
    3Factor  0.000841  1.000455 -0.310717 -0.466917   0.722349
    

R제곱 값을 보면, 0.698에서 0.722로 증가
즉, 개별 주식 수익률에 대한 설명력이 3팩터모델이 더 좋다는 것을 의미

삼성전자는 대형주이기 때문에 SMB와 음의 상관관계를 보임
HML과 음의 상관관계가 있다는 것은 과거 삼성전자의 PBR이 높았기 때문으로 추정

(2000-2016년 삼성전자 일일 PBR데이터 평균 값: 1.72)
