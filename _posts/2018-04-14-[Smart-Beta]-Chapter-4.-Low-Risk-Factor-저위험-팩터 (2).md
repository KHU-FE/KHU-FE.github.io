---
bg: "smartbeta.jpg"
layout: post
title:  "[Smart Beta] Chapter 4. Low Risk Factor : 저위험 팩터 (2)"
crawlertitle: "[Smart Beta] Low Risk Factor"
summary: "저변동성 효과의 검증을 위한 백터스트"
date:   2018-04-14 20:09:47 +0700
categories: posts
tags: ['Smart-Beta']
author: KHU-FE
---

[SMART BETA] Low Risk Factor 저위험 팩터 (2)
====================

## BackTesting
실제 국내 시장에서의 저변동성 효과의 확인을 위해서 백테스트를 활용하여 검증한다.

* Methodology
  활용 데이터 : KOSPI200에서 2000년부터 2016년까지의 월별 데이터가 존재하는 종목들을 사용
  
  Step 1. 현재 시점 t에서 과거 n개월을 기준으로 수익률의 표준편차를 계산

  Step 2. 계산된 표준편차가 가장 작은 종목부터 가장 큰 종목까지의 순서를 활용하여 각 N개의 포트폴리오에 포함될 개별자산을 선정
  
  Step 3. 향후 투자기간 k에 대하여 t+k 시점까지 투자할 포트폴리오 N개를 위 방법을 통하여 구성
  
  Step 4. 위 작업을 k 기간마다 반복하여 rebalancing
  
  Step 5. 구성된 포트폴리오의 투자 수익률을 그래프를 통해 확인한다.

### 1. Data


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from scipy import stats
from matplotlib.pyplot import rc
rc('font',family='Malgun Gothic')
```


```python
data = pd.read_excel("D:\\제용\\금융공학 연구실\\18-1학기\\스마트베타\\데이터\\ch4_data.xlsx", sheetname='Sheet4', index_col=0)/100
```


```python
data.tail()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CJ</th>
      <th>CJ대한통운</th>
      <th>DB손해보험</th>
      <th>DB하이텍</th>
      <th>GS건설</th>
      <th>JW중외제약</th>
      <th>KCC</th>
      <th>LG</th>
      <th>LG상사</th>
      <th>LS</th>
      <th>...</th>
      <th>현대차</th>
      <th>현대해상</th>
      <th>호텔신라</th>
      <th>효성</th>
      <th>삼성SDI</th>
      <th>삼성전기</th>
      <th>삼성전자</th>
      <th>삼성중공업</th>
      <th>삼성증권</th>
      <th>삼성화재</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-08-31</th>
      <td>-0.0474</td>
      <td>0.0025</td>
      <td>0.0630</td>
      <td>-0.0421</td>
      <td>0.0017</td>
      <td>0.2474</td>
      <td>0.0456</td>
      <td>0.0378</td>
      <td>-0.0878</td>
      <td>0.1801</td>
      <td>...</td>
      <td>0.0076</td>
      <td>0.1155</td>
      <td>0.1376</td>
      <td>-0.0504</td>
      <td>0.0948</td>
      <td>-0.0362</td>
      <td>0.0526</td>
      <td>-0.0261</td>
      <td>-0.0728</td>
      <td>0.0131</td>
    </tr>
    <tr>
      <th>2016-09-30</th>
      <td>-0.0262</td>
      <td>0.0721</td>
      <td>0.0104</td>
      <td>0.0440</td>
      <td>0.0209</td>
      <td>0.2080</td>
      <td>-0.0412</td>
      <td>-0.0182</td>
      <td>0.0496</td>
      <td>-0.0747</td>
      <td>...</td>
      <td>0.0188</td>
      <td>0.0725</td>
      <td>-0.1077</td>
      <td>-0.0152</td>
      <td>-0.1688</td>
      <td>-0.0808</td>
      <td>-0.0136</td>
      <td>0.1153</td>
      <td>-0.0128</td>
      <td>0.0352</td>
    </tr>
    <tr>
      <th>2016-10-31</th>
      <td>-0.0618</td>
      <td>-0.0673</td>
      <td>0.0425</td>
      <td>-0.0421</td>
      <td>-0.0869</td>
      <td>-0.4351</td>
      <td>0.0228</td>
      <td>-0.0526</td>
      <td>-0.2020</td>
      <td>-0.0567</td>
      <td>...</td>
      <td>0.0332</td>
      <td>-0.0248</td>
      <td>-0.0529</td>
      <td>0.0308</td>
      <td>-0.0177</td>
      <td>-0.0368</td>
      <td>0.0257</td>
      <td>0.0405</td>
      <td>-0.0015</td>
      <td>0.0429</td>
    </tr>
    <tr>
      <th>2016-11-30</th>
      <td>0.0057</td>
      <td>-0.0846</td>
      <td>0.0267</td>
      <td>-0.0880</td>
      <td>-0.1175</td>
      <td>-0.0061</td>
      <td>-0.0791</td>
      <td>-0.0783</td>
      <td>0.0826</td>
      <td>0.1093</td>
      <td>...</td>
      <td>-0.0500</td>
      <td>0.0297</td>
      <td>-0.1204</td>
      <td>0.0373</td>
      <td>-0.0212</td>
      <td>-0.0170</td>
      <td>0.0653</td>
      <td>-0.1487</td>
      <td>-0.0723</td>
      <td>0.0051</td>
    </tr>
    <tr>
      <th>2016-12-31</th>
      <td>0.0655</td>
      <td>-0.0272</td>
      <td>-0.1438</td>
      <td>0.0225</td>
      <td>0.1205</td>
      <td>0.0987</td>
      <td>-0.0349</td>
      <td>0.0619</td>
      <td>-0.0299</td>
      <td>-0.0263</td>
      <td>...</td>
      <td>0.0977</td>
      <td>-0.1346</td>
      <td>-0.0446</td>
      <td>0.0468</td>
      <td>0.1809</td>
      <td>0.0972</td>
      <td>0.0321</td>
      <td>0.1144</td>
      <td>-0.0109</td>
      <td>-0.0836</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 91 columns</p>
</div>



다음과 같이 기간상의 문제나 오류가 없는 KOSPI200의 개별 종목들의 데이터를 활용합니다.

### 2. Slicing tools


```python
def how_many_times(data, times, rebal = 1):
    #repet_times = len(data)-times
    start_period = np.array(list(map(lambda x: x, range(len(data.index)-times+1))))
    end_period = np.array(list(map(lambda x: x+times-1, range(len(data.index)-times+1))))
    
    period_list = list(map(lambda x: x*rebal, range(int(np.ceil(len(start_period)/rebal)))))
    return start_period[period_list], end_period[period_list]  
```

각 데이터에 대한 과거 수익률을 얻기 위해서 rebalancing 시기마다의 backtesting 기간의 데이터를 slicing하기 위하여 인덱스의 순서를 뽑아주는 function을 정의합니다.


```python
start_period, end_period = how_many_times(data, 60, 3)

end_period
```




    array([ 59,  62,  65,  68,  71,  74,  77,  80,  83,  86,  89,  92,  95,
            98, 101, 104, 107, 110, 113, 116, 119, 122, 125, 128, 131, 134,
           137, 140, 143, 146, 149, 152, 155, 158, 161, 164, 167, 170, 173,
           176, 179, 182, 185, 188, 191, 194, 197, 200, 203, 206, 209, 212,
           215, 218, 221, 224, 227, 230, 233, 236, 239, 242, 245, 248, 251,
           254, 257, 260, 263])



다음과 같이 데이터에서 60개월의 데이터를 이용하여 backtesting을 하고 매 3개월 마다 이 작업을 반복하여 rebalancing을 하는 경우를 상정한 예시에서는 data의 index를 추출하기 위한 리스트를 반환받습니다.


```python
def how_many_stocks(data, many):
    N = len(data.columns)
    num_of_stock = int(N/many)
    
    initial = list(map(lambda many: (num_of_stock*many), range(many)))
    end = list(map(lambda many: num_of_stock+(num_of_stock*many), range(many)))

    if end[-1] != N:
        end[-1] = N-1
        
    return initial, end
```

위에서 기간을 slicing 하기 위하여 function을 구성하였던 것처럼 각 포트폴리오에 편입될 개별 주식들을 slicing 하기 위한 function을 구성합니다.


```python
initial, end = how_many_stocks(data, 5)

end
```




    [18, 36, 54, 72, 90]



위에서처럼 5개의 포트폴리오를 구성한다고 한다면 slicing 하기 위해 얻을 수 있는 column index의 순서 리스트를 다음과 반환받습니다.

### 3. Calculation tools

첫 번째, backtesting에 활용할 수익률과 이후 투자기간에 대한 데이터를 각각 DataFrame의 형태를 통하여 input data로 받으면 N개의 포트폴리오의 구성기준(표준편차)에 따라서 각 포트폴리오의 편입될 자산들만을 포함한 DataFrame N개를 리스트의 형태로 반환합니다.


```python
def df_return(data, data2, initial, end):
    split_std = np.std(data)
    std_sort = split_std.sort_values()
    std_index = list(std_sort.index)
    
    df_list = list()
    for j in range(len(end)):
        stock_split_list = std_index[initial[j]:end[j]]
        stock_split_data = data2[stock_split_list]
        df_list.append(stock_split_data)
        
    return df_list
```

두 번째, 각 포트폴리오에 속하는 자산들만을 포함한 DataFrame 리스트를 input으로 입력받아 해당 포트폴리오의 구성방식(동일가중)의 형태로 수익률을 계산해 각 포트폴리오의 해당 투자기간동안의 수익률을 반환하는 function을 구성합니다.


```python
def portfolio_generater(list_data, method = 'eq'):
    portfolio_df = pd.DataFrame()
    for i in range(len(list_data)):
        ret_data = list_data[i]+1
        portfolio = np.mean(np.cumprod(ret_data), axis=1)
     
        num_port = 'portfolio'+str(i+1)
        portfolio_df.loc[:,num_port] = portfolio[-1:]
    return portfolio_df
```

### 4. Backtesting function

2번 slicing tools, 3번 calculation tools에서 구성한 함수를 통하여 backtesting을 하는 함수를 구성합니다.


```python
def period_split_std(data, times, rebal, many):
    start_period, end_period = how_many_times(data, times, rebal)
    initial, end = how_many_stocks(data, many)
    
    total_list = list()
    
    for i in range(len(start_period)-1):
        data_split = data.ix[data.index[start_period[i]]:data.index[end_period[i]]]
        data_split_plus = data.ix[data.index[start_period[i+1]]:data.index[end_period[i+1]]]
        data_split_2 = data_split_plus.ix[data_split_plus.index[-rebal]:,]
        
        list_data = df_return(data_split, data_split_2, initial, end)
        
        n_period_port = portfolio_generater(list_data, rebal)
        
        total_list.append(n_period_port)
       
    result = pd.concat(total_list)
    
    return result
```

다음과 같이 데이터와 backtesting에 활용할 기간, rebalancing 기간, 포트폴리오의 수를 input으로 받아 전체 기간에서의 backtesting을 통한 각 포트폴리오의 수익률을 values로 가지는 DataFrame을 반환받습니다.

### 5. Backtesting


```python
portfolio_ret = period_split_std(data, 60, 3, 8)

portfolio_ret.tail()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>portfolio1</th>
      <th>portfolio2</th>
      <th>portfolio3</th>
      <th>portfolio4</th>
      <th>portfolio5</th>
      <th>portfolio6</th>
      <th>portfolio7</th>
      <th>portfolio8</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-12-31</th>
      <td>1.071693</td>
      <td>0.961979</td>
      <td>0.931851</td>
      <td>0.969705</td>
      <td>0.967224</td>
      <td>0.948853</td>
      <td>0.992491</td>
      <td>0.960513</td>
    </tr>
    <tr>
      <th>2016-03-31</th>
      <td>1.003132</td>
      <td>1.066610</td>
      <td>1.040075</td>
      <td>0.948614</td>
      <td>1.048135</td>
      <td>1.104522</td>
      <td>1.051418</td>
      <td>1.156299</td>
    </tr>
    <tr>
      <th>2016-06-30</th>
      <td>0.935060</td>
      <td>0.956624</td>
      <td>0.978946</td>
      <td>0.909576</td>
      <td>0.956315</td>
      <td>0.994503</td>
      <td>0.959145</td>
      <td>1.248704</td>
    </tr>
    <tr>
      <th>2016-09-30</th>
      <td>1.022513</td>
      <td>0.990528</td>
      <td>0.995827</td>
      <td>1.052481</td>
      <td>1.044531</td>
      <td>0.999911</td>
      <td>0.998524</td>
      <td>0.982455</td>
    </tr>
    <tr>
      <th>2016-12-31</th>
      <td>0.986534</td>
      <td>1.015902</td>
      <td>0.959997</td>
      <td>0.986215</td>
      <td>0.912742</td>
      <td>0.989903</td>
      <td>0.995799</td>
      <td>0.892104</td>
    </tr>
  </tbody>
</table>
</div>



다음과 같이 backtesting 기간 60개월, 3개월 마다 rebalancing, 8개의 포트폴리오의 수익률을 반환하는 DataFrame을 받습니다.


```python
invest_df = np.cumprod(portfolio_ret)

invest_df.tail()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>portfolio1</th>
      <th>portfolio2</th>
      <th>portfolio3</th>
      <th>portfolio4</th>
      <th>portfolio5</th>
      <th>portfolio6</th>
      <th>portfolio7</th>
      <th>portfolio8</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-12-31</th>
      <td>22.089027</td>
      <td>15.323978</td>
      <td>17.044369</td>
      <td>11.456239</td>
      <td>25.974302</td>
      <td>15.543262</td>
      <td>19.190994</td>
      <td>9.313575</td>
    </tr>
    <tr>
      <th>2016-03-31</th>
      <td>22.158216</td>
      <td>16.344703</td>
      <td>17.727426</td>
      <td>10.867547</td>
      <td>27.224579</td>
      <td>17.167881</td>
      <td>20.177751</td>
      <td>10.769276</td>
    </tr>
    <tr>
      <th>2016-06-30</th>
      <td>20.719269</td>
      <td>15.635739</td>
      <td>17.354186</td>
      <td>9.884856</td>
      <td>26.035282</td>
      <td>17.073513</td>
      <td>19.353394</td>
      <td>13.447639</td>
    </tr>
    <tr>
      <th>2016-09-30</th>
      <td>21.185727</td>
      <td>15.487638</td>
      <td>17.281759</td>
      <td>10.403620</td>
      <td>27.194658</td>
      <td>17.071992</td>
      <td>19.324833</td>
      <td>13.211694</td>
    </tr>
    <tr>
      <th>2016-12-31</th>
      <td>20.900433</td>
      <td>15.733920</td>
      <td>16.590433</td>
      <td>10.260205</td>
      <td>24.821714</td>
      <td>16.899622</td>
      <td>19.243650</td>
      <td>11.786201</td>
    </tr>
  </tbody>
</table>
</div>



위에서 얻은 수익률을 np.cumprod()함수를 활용하여 누적수익률을 계산합니다.


```python
def cum_plot(data, title):
    data.index = pd.to_datetime(data.index, format="%Y%m")
    plt.figure(figsize=(20,7))
    plt.plot(data)
    plt.title(title)
    plt.axhline(color = 'k')

    ax = plt.gca()
    ax.spines['right'].set_color('none')
    ax.spines['top'].set_color('none')
    ax.spines['bottom'].set_color('none')
    ax.spines['left'].set_color('none')
    ax.legend(data.columns.values)

    plt.show()
```

데이터를 좀 더 보기 쉽게 plotting 해주는 function을 새로 구성해주면 결과를 그래프의 형태로 확인할 수 있습니다.

# Conclustion


```python
cum_plot(invest_df, "변동성 포트폴리오 누적 수익률(2000~2016)")
```


[![png]({{ site.images | relative_url }}/output_34_0.png)]({{ site.images | relative_url }}/output_34_0.png)


다음과 같이 일반적으로 생각하는 가장 높은 risk를 보유한 portfolio8의 경우에 오히려 장기적으로 보았을 경우 변동성의 비하여 낮은 수익률을 기록하는 저변동성 효과를 확인할 수 있습니다.
반대로 가장 낮은 변동성의 개별 주식들로 구성된 포트폴리오의 경우에는 장기적으로 높은 수준의 수익률을 기록하는 것을 확인할 수 있습니다.

대부분의 포트폴리오들이 risk가 낮은 장기적으로 높은 수익률을 확보하는 경향을 backtesting을 통해 확인할 수 있습니다.
