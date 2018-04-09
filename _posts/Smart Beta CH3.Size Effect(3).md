
# 소형주 효과를 반영한 시가총액가중기준 소형 CMA - 대형 CMA 누적수익률


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```


```python
data_glo = pd.read_excel('/Users/taehyeon/pythonfiles/data/시가총액가중기준CMA.xlsx', sheet_name=0,index_col=0)
data_exus = pd.read_excel('/Users/taehyeon/pythonfiles/data/시가총액가중기준CMA.xlsx', sheet_name=1,index_col=0)
data_eu = pd.read_excel('/Users/taehyeon/pythonfiles/data/시가총액가중기준CMA.xlsx', sheet_name=2,index_col=0)
data_jpn = pd.read_excel('/Users/taehyeon/pythonfiles/data/시가총액가중기준CMA.xlsx', sheet_name=3,index_col=0)
data_exjpn = pd.read_excel('/Users/taehyeon/pythonfiles/data/시가총액가중기준CMA.xlsx', sheet_name=4,index_col=0)
```


```python
print(data_glo.head(3))
print(data_exus.head(3))
```

                SMALL LoINV  ME1 INV2  SMALL HiINV  BIG LoINV  ME2 INV2  BIG HiINV
    1990-07-01         2.26      1.98         1.06       2.41      1.01       0.47
    1990-08-01       -10.96    -10.78       -12.13      -9.22    -10.11     -10.01
    1990-09-01        -9.86     -9.60       -10.80      -9.83    -10.75     -13.15
                SMALL LoINV  ME1 INV2  SMALL HiINV  BIG LoINV  ME2 INV2  BIG HiINV
    1990-07-01         4.98      4.81         4.89       2.94      1.92       1.81
    1990-08-01       -10.85    -10.78       -11.29     -10.37    -10.44      -9.87
    1990-09-01       -10.73    -10.76       -11.34     -13.55    -14.14     -14.55
    


```python
print(data_glo.tail(3))
```

                SMALL LoINV  ME1 INV2  SMALL HiINV  BIG LoINV  ME2 INV2  BIG HiINV
    2017-12-01         2.78      1.57         2.37       2.26      1.04       1.00
    2018-01-01         4.60      3.44         4.27       5.06      4.91       6.35
    2018-02-01        -3.58     -3.53        -2.89      -5.36     -4.16      -2.41
    


```python
d1 = (data_glo['SMALL LoINV']-data_glo['SMALL HiINV']) - (data_glo['BIG LoINV']-data_glo['BIG HiINV'])
d2 = (data_exus['SMALL LoINV']-data_exus['SMALL HiINV']) - (data_exus['BIG LoINV']-data_exus['BIG HiINV'])
d3 = (data_eu['SMALL LoINV']-data_eu['SMALL HiINV']) - (data_eu['BIG LoINV']-data_eu['BIG HiINV'])
d4 = (data_jpn['SMALL LoINV']-data_jpn['SMALL HiINV']) - (data_jpn['BIG LoINV']-data_jpn['BIG HiINV'])
d5 = (data_exjpn['SMALL LoINV']-data_exjpn['SMALL HiINV']) - (data_exjpn['BIG LoINV']-data_exjpn['BIG HiINV'])
```


```python
print(d1.head(2))
print(d1.tail(2))
```

    1990-07-01   -0.74
    1990-08-01    0.38
    dtype: float64
    2018-01-01    1.62
    2018-02-01    2.26
    dtype: float64
    


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

    1990-07-01   -0.007400
    1990-08-01   -0.003628
    dtype: float64
    2018-01-01    0.759363
    2018-02-01    0.799125
    dtype: float64
    


```python
x = pd.DataFrame({'Global': d1,
                 'Global ex US': d2,
                 'Europe': d3,
                 'Japan': d4,
                 'Asia ex Japan': d5
                 })
x = x[['Global', 'Global ex US', 'Europe', 'Japan', 'Asia ex Japan']]
print(x.head())
print(x.tail())
```

                  Global  Global ex US    Europe     Japan  Asia ex Japan
    1990-07-01 -0.007400     -0.010400  0.000700 -0.009300      -0.002900
    1990-08-01 -0.003628     -0.001098 -0.009507  0.024384      -0.011674
    1990-09-01 -0.027342     -0.004993 -0.013073  0.032784      -0.035691
    1990-10-01 -0.013044     -0.011262 -0.008730 -0.058308      -0.000397
    1990-11-01 -0.031105     -0.007010 -0.006351 -0.012071       0.003401
                  Global  Global ex US    Europe     Japan  Asia ex Japan
    2017-10-01  0.775634      0.433035  0.623264 -0.165711       1.161845
    2017-11-01  0.746158      0.410393  0.594370 -0.161122       1.219567
    2017-12-01  0.731316      0.394738  0.594530 -0.178990       1.206471
    2018-01-01  0.759363      0.377024  0.571090 -0.178826       1.208016
    2018-02-01  0.799125      0.378126  0.562763 -0.201983       1.259683
    


```python
ax = x.plot(figsize=(12,8))
ax.set_xlim(pd.Timestamp('1990-07'), pd.Timestamp('2018-02'))
ax.set_ylim(-1, 2)
ax.axhline(0, color='black',lw=0.8)
ax.set_title('Average value Weighted')

ax
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1114a54e0>




![png](output_10_1.png)


# 소형주 효과를 반영한 동일가중기준 소형 CMA - 대형 CMA 누적수익률


```python
data2_glo = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중기준CMA.xlsx', sheet_name=0,index_col=0)
data2_exus = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중기준CMA.xlsx', sheet_name=1,index_col=0)
data2_eu = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중기준CMA.xlsx', sheet_name=2,index_col=0)
data2_jpn = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중기준CMA.xlsx', sheet_name=3,index_col=0)
data2_exjpn = pd.read_excel('/Users/taehyeon/pythonfiles/data/동일가중기준CMA.xlsx', sheet_name=4,index_col=0)
```


```python
print(data2_glo.head(2))
print(data2_glo.tail(2))
```

                SMALL LoINV  ME1 INV2  SMALL HiINV  BIG LoINV  ME2 INV2  BIG HiINV
    1990-07-01         2.16      1.94         1.23       2.98      2.32       1.91
    1990-08-01        -7.40     -9.30        -9.97     -11.40    -11.47     -11.96
                SMALL LoINV  ME1 INV2  SMALL HiINV  BIG LoINV  ME2 INV2  BIG HiINV
    2018-01-01         7.11      4.74         6.15       4.97      4.47       4.67
    2018-02-01        -3.64     -3.08        -4.01      -4.63     -3.97      -2.86
    


```python
data1 = (data2_glo['SMALL LoINV']-data2_glo['SMALL HiINV']) - (data2_glo['BIG LoINV']-data2_glo['BIG HiINV'])
data2 = (data2_exus['SMALL LoINV']-data2_exus['SMALL HiINV']) - (data2_exus['BIG LoINV']-data2_exus['BIG HiINV'])
data3 = (data2_eu['SMALL LoINV']-data2_eu['SMALL HiINV']) - (data2_eu['BIG LoINV']-data2_eu['BIG HiINV'])
data4 = (data2_jpn['SMALL LoINV']-data2_jpn['SMALL HiINV']) - (data2_jpn['BIG LoINV']-data2_jpn['BIG HiINV'])
data5 = (data2_exjpn['SMALL LoINV']-data2_exjpn['SMALL HiINV']) - (data2_exjpn['BIG LoINV']-data2_exjpn['BIG HiINV'])
```


```python
data1 = (1 + data1/100).cumprod() - 1
data2 = (1 + data2/100).cumprod() - 1
data3 = (1 + data3/100).cumprod() - 1
data4 = (1 + data4/100).cumprod() - 1
data5 = (1 + data5/100).cumprod() - 1
```


```python
y = pd.DataFrame({'Global': data1,
                 'Global ex US': data2,
                 'Europe': data3,
                 'Japan': data4,
                 'Asia ex Japan': data5
                 })
y = y[['Global', 'Global ex US', 'Europe', 'Japan', 'Asia ex Japan']]
print(y.head(2))
print(y.tail(2))
```

                  Global  Global ex US    Europe     Japan  Asia ex Japan
    1990-07-01 -0.001400      -0.00210 -0.003300 -0.010900       0.022000
    1990-08-01  0.018672       0.02644  0.010654  0.013234       0.042644
                  Global  Global ex US    Europe     Japan  Asia ex Japan
    2018-01-01  3.413879      3.379474  1.838120  0.233228      14.190292
    2018-02-01  3.508336      3.478012  1.914465  0.240751      14.065732
    


```python
ax1 = y.plot(figsize=(10,6))
ax1.set_xlim(pd.datetime(1990,7,1), pd.datetime(2018,2,1))
ax1.set_ylim(-0.5, 15)
ax1.axhline(0, color='black',lw=0.8)
ax1.set_title('Average equal Weighted')

ax1
```




    <matplotlib.axes._subplots.AxesSubplot at 0x112f02198>




![png](output_17_1.png)

