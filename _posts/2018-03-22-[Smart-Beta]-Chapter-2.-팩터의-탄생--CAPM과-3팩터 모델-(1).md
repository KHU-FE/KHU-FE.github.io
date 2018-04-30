---
bg: "tools.jpg"
layout: post
title:  "[Smart Beta] Chapter 2. 팩터의 탄생 : CAPM과 3팩터 모델 (1)"
crawlertitle: "[Smart Beta] CAPM & 3-Factor model"
summary: "자본시장선(CML, Capital Market Line)과 3-Factor model"
date:   2018-03-22 20:09:47 +0700
categories: posts
tags: ['Smart-Beta']
author: KHU-FE
---
# 팩터의 탄생 : CAPM과 3팩터 모델


## 시장베타의 탄생, 자산가격결정모형(CAPM)

### CAPM(Capital Assets Pricing Model, 자본자산 가격결정 모형) 

\$R_i = R_f + B_i x [R_m-R_f]$\

 : 투자자들이 투자활동을 하여 시장 전체가 균형상태에 있을 때 주식 등 자본자산의 균형가격은 어떻게 결정되는 가에 관한 모형이라 할 수 있다. 협의로는 증권시장선(SML, Security Market Line)만을 의미하나, 광의로는 자본시장선(CML, Capital Market Line) 과 증권특성선(SCL, Security Characteristic Line)까지 포함한다.



```python
import pandas as pd
from scipy import stats, polyval, polyfit
from pylab import plot, title, show, legend

data1 = pd.read_excel('C:\\Users\\이한송\\Desktop\\연구실\\data2.xlsx',index_row=0)
x = data1['코스피']
y = data1['삼성전자']
```

### 기울기, y절편, r-값, p-값, 추정 오차 구하기


```python
slope, intercept, r, p, stderr = stats.linregress(x,y)
ry = polyval([slope, intercept], x) #추정오차
# print(polyfit(x,y,1)) #기울기랑 y절편만 나오는 함수

print("기울기 = ", slope)
print("y절편 = ", intercept)
print("r-value = ", r)
print("p-value = ", p)
print("표준 오차 = ", stderr)
print("추정 오차 = ", ry)
```

    기울기 =  1.1819687821455458
    y절편 =  0.00040203012836770557
    r-value =  0.7693353333620755
    p-value =  0.0
    표준 오차 =  0.015144721607603488
    추정 오차 =  [ 0.03597929 -0.08079923 -0.03021096 ...  0.00300236 -0.0098811
      0.001584  ]
    

### [그림2.2]삼성전자와 KOSPI 수익률 관계


```python
import matplotlib.pyplot as plt
plt.figure(figsize=(8,4))

plot(x,y,'k.') #실제 데이터
plot(x,ry,'r-') #회귀선
title('Samsung & KOSPI regression (2000~2016)')
legend(['original','regression'])
show()
```


[![png]({{ site.images | relative_url }}/output_6_0.png)]({{ site.images | relative_url }}/output_6_0.png)


### 삼성전자&KOSPI 회귀분석 식



```python
import numpy as np

corr = np.corrcoef(x,y)[1,0]
std_x = np.std(x)
std_y = np.std(y)
mean_x = np.mean(x)
mean_y = np.mean(y)
    
beta = corr*std_y / std_x
alpha = mean_y - beta*mean_x
estimated_y = alpha + beta*x

plt.figure(figsize=(8,4))
title('regression')
plot(x,y,'k.') #실제데이터
plot(x,estimated_y,'r-') #회귀선
legend(['original','regression'])
show()
```


[![png]({{ site.images | relative_url }}/output_8_0.png)]({{ site.images | relative_url }}/output_8_0.png)


### 삼성전자&KOSPI CAPM


```python
z1 = (data1['코스피']-data1['Rf'])
z2 = (data1['삼성전자']-data1['Rf'])

slope, intercept, r, p, std = stats.linregress(z1,z2)
ry = polyval([slope, intercept], z1)

plt.figure(figsize=(8,4))
plot(z1, z2,'k.') #실제 데이터
plot(z1, ry,'r-') #회귀선

title('samsung & KOSPI CAPM (2000~2016)')
legend(['original','regression'])
show()
```


[![png]({{ site.images | relative_url }}/output_10_0.png)]({{ site.images | relative_url }}/output_10_0.png)

### [표 2.1] 삼성전자와 KOSPI 수익률 회귀분석 결과


```python
regression = [intercept, slope, r*r]
df = pd.DataFrame([regression],index=list('값'),columns=['초과 수익률', 'B(MKT)', 'R Square'])
df.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>초과 수익률</th>
      <th>B(MKT)</th>
      <th>R Square</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>값</th>
      <td>0.003756</td>
      <td>1.087768</td>
      <td>0.698056</td>
    </tr>
  </tbody>
</table>
</div>


