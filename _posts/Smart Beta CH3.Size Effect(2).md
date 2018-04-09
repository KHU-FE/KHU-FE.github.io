
## B. 동일가중기준

## 데이터 불러오기 및 데이터 확인하기

* pandas는 표 형식의 자료를 DataFrame 객체로 읽어오는 기능을 제공한다.
   
    1. pandas의 read_excel 함수를 이용하여 데이터를 불러온다.
    2. 불러온 데이터의 값을 확인하기 위해 데이터객체명.head() 혹은 .tail()을 이용하여 불러온 데이터를 확인한다.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```


```python
df_glo = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중 기준 소형 RMW- 대형 RMW.xlsx',sheet_name=0, index_col=0)
df_exus = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중 기준 소형 RMW- 대형 RMW.xlsx',sheet_name=1, index_col=0)
df_eu = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중 기준 소형 RMW- 대형 RMW.xlsx',sheet_name=2, index_col=0)
df_jpn = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중 기준 소형 RMW- 대형 RMW.xlsx',sheet_name=3, index_col=0)
df_exjpn = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중 기준 소형 RMW- 대형 RMW.xlsx',sheet_name=4, index_col=0)

```


```python
print(df_glo.head(2))
print(df_glo.tail(2))
```

                SMALL LoOP  SMALL HiOP  BIG LoOP  BIG HiOP
    1990-07-01        1.94        3.32      2.36      2.82
    1990-08-01       -7.85      -10.03    -11.61    -11.78
                SMALL LoOP  SMALL HiOP  BIG LoOP  BIG HiOP
    2018-01-01        6.51        5.38      4.99      4.43
    2018-02-01       -4.05       -2.14     -3.86     -3.71
    

## 소형 RMW - 대형 RMW


```python
d1 = (df_glo['SMALL HiOP'] - df_glo['SMALL LoOP']) - (df_glo['BIG HiOP'] - df_glo['BIG LoOP'])
d2 = (df_exus['SMALL HiOP'] - df_exus['SMALL LoOP']) - (df_exus['BIG HiOP'] - df_exus['BIG LoOP'])
d3 = (df_eu['SMALL HiOP'] - df_eu['SMALL LoOP']) - (df_eu['BIG HiOP'] - df_eu['BIG LoOP'])
d4 = (df_jpn['SMALL HiOP'] - df_jpn['SMALL LoOP']) - (df_jpn['BIG HiOP'] - df_jpn['BIG LoOP'])
d5 = (df_exjpn['SMALL HiOP'] - df_exjpn['SMALL LoOP']) - (df_exjpn['BIG HiOP'] - df_exjpn['BIG LoOP'])
```


```python
print(d1.head(2))
print(d1.tail(2))
```

    1990-07-01    0.92
    1990-08-01   -2.01
    dtype: float64
    2018-01-01   -0.57
    2018-02-01    1.76
    dtype: float64
    

## 소형 RMW - 대형 RMW 누적 수익률 연산


```python
d1 = (1 + d1/100).cumprod() - 1
d2 = (1 + d2/100).cumprod() - 1
d3 = (1 + d3/100).cumprod() - 1
d4 = (1 + d4/100).cumprod() - 1
d5 = (1 + d5/100).cumprod() - 1
```


```python
print(d1.head(2))
print(d1.tail(2))
```

    1990-07-01    0.009200
    1990-08-01   -0.011085
    dtype: float64
    2018-01-01   -0.554678
    2018-02-01   -0.546841
    dtype: float64
    

## 연산된 데이터 정리하기


```python
x = pd.DataFrame({'Global': d1,
                 'Global ex US': d2,
                 'Europe': d3,
                 'Japan': d4,
                 'Asia ex Japan': d5
                 })
x = x[['Global','Global ex US','Europe', 'Japan','Asia ex Japan']]
print(x.head())
print(x.tail())
```

                  Global  Global ex US    Europe     Japan  Asia ex Japan
    1990-07-01  0.009200      0.011200  0.016700  0.034900      -0.019800
    1990-08-01 -0.011085     -0.012867  0.011108  0.028898      -0.055675
    1990-09-01 -0.031358     -0.024712 -0.005171  0.020564      -0.045854
    1990-10-01 -0.000071      0.014007  0.018208  0.003520      -0.005780
    1990-11-01 -0.007470      0.002041  0.003749 -0.004006      -0.026559
                  Global  Global ex US    Europe     Japan  Asia ex Japan
    2017-10-01 -0.543415     -0.506970  0.011607 -0.195181      -0.930436
    2017-11-01 -0.551542     -0.509830  0.004020 -0.188259      -0.932099
    2017-12-01 -0.552125     -0.504928  0.020285 -0.159929      -0.931447
    2018-01-01 -0.554678     -0.507206  0.008042 -0.152705      -0.930624
    2018-02-01 -0.546841     -0.499025  0.021550 -0.144740      -0.929473
    


```python
ax2 = x.plot(figsize=(12,8))
ax2.set_xlim(pd.Timestamp('1990-07'), pd.Timestamp('2018-02'))
ax2.set_ylim(-1, 2)
ax2.axhline(0, color='black',lw=0.8)
ax2.set_title('Average Equal Weighted')

ax2
```




    <matplotlib.axes._subplots.AxesSubplot at 0x10962c048>




![png](output_13_1.png)

