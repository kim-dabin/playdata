# DAY82

## pandas :데이터  읽기

- CSV 형식의 데이터 읽기 : read_csv ()
- Excel 파일 읽기 : read_excel ()
- JSON 문자열의 읽기 : read_json ()
- pickle 형식 (Python 객체를 저장하는 형식)에 의한 데이터의 읽기 / 쓰기 : read_pickle () to_pickle ()
- 인터넷에서 제공되는 주식 시세 읽기 : pandas-datareader

### 데이터의 특징을 파악

- 데이터의 특징을 표시 : DataFrame.info ()
- 요약 통계를 표시 : DataFrame.describe ()
- 데이터의 일부를 표시 : DataFrame.head () DataFrame.tail ()
- 데이터의 모양을 표시 : DataFrame.shape
- 행수를 취득 : DataFrame.index.values
- 열 이름 가져 오기 : DataFrame.columns.values
- 각각의 열의 형태를 얻을 : DataFrame.dtypes

### 데이터 조각

- 데이터의 조각 : DataFrame [], DataFrame.iloc [], DataFrame.loc []
- 필터링 : DataFrame.query ()

### 데이터 정렬

- 값으로 정렬 : DataFrame.sort_values ()
- 인덱스에 의한 정렬 : DataFrame.sort_index ()
- 열 이름 바꾸기 : DataFrame.rename ()

### 집계

- 열 집계 : DataFrame.value_counts ()
- 표 집계 : DataFrame.groupby ()
- ※ groupby () sum ()에서 총 groupby (). mean ()에서 평균 얻을 수있다


```python
# -*- coding:utf-8 -*-
#Pandas 및 필요한 라이브러리 가져오기 
import pandas as pd
from pandas import DataFrame
import numpy as np
#샘플 데이터 집합을 검색하는 코드 
from sklearn import datasets
boston = datasets.load_boston()
print(type(boston))
```

    <class 'sklearn.utils.Bunch'>


- pandas를 이용해서 dataframe으로 변환후 작업 
    - class pandas.DataFrame(data=None, index=None, columns=None, dtype=None, copy=False)[source]


```python
df = DataFrame(boston.data, columns=boston.feature_names)
print(df, type(df))
```

            CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
    0    0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0   
    1    0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0   
    2    0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0   
    3    0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0   
    4    0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0   
    ..       ...   ...    ...   ...    ...    ...   ...     ...  ...    ...   
    501  0.06263   0.0  11.93   0.0  0.573  6.593  69.1  2.4786  1.0  273.0   
    502  0.04527   0.0  11.93   0.0  0.573  6.120  76.7  2.2875  1.0  273.0   
    503  0.06076   0.0  11.93   0.0  0.573  6.976  91.0  2.1675  1.0  273.0   
    504  0.10959   0.0  11.93   0.0  0.573  6.794  89.3  2.3889  1.0  273.0   
    505  0.04741   0.0  11.93   0.0  0.573  6.030  80.8  2.5050  1.0  273.0   
    
         PTRATIO       B  LSTAT  
    0       15.3  396.90   4.98  
    1       17.8  396.90   9.14  
    2       17.8  392.83   4.03  
    3       18.7  394.63   2.94  
    4       18.7  396.90   5.33  
    ..       ...     ...    ...  
    501     21.0  391.99   9.67  
    502     21.0  396.90   9.08  
    503     21.0  396.90   5.64  
    504     21.0  393.45   6.48  
    505     21.0  396.90   7.88  
    
    [506 rows x 13 columns] <class 'pandas.core.frame.DataFrame'>


- 변수를 추가해서 데이터를 탐색한다 


```python
df['MEDV']=np.array(boston.target)

#데이터 개요(제목)를 확인하자 
print(df.head())

#행렬 수 
print(df.shape) #(506,14)

print('------------------세부 정보--------------------')
#세부 정보 
print(df.info())
print('------------------통계 정보 --------------------')
#데이터 통계 정보 
print(df.describe())
print(df.MEDV.mean(), df.MEDV.median(), df.MEDV.var())

#그래프 적용
res = df.MEDV[:50].hist()
print(df.MEDV.astype(float))

#테이블의 일부 내용 검출 
#데이터 조각: DataFrame[], DataFrame.iloc[], DataFrame.loc[], DataFrame.ix
#필터링 : DataFrame.query()
print(df.loc[:9][['AGE','MEDV']])
```

          CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
    0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0   
    1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0   
    2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0   
    3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0   
    4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0   
    
       PTRATIO       B  LSTAT  MEDV  
    0     15.3  396.90   4.98  24.0  
    1     17.8  396.90   9.14  21.6  
    2     17.8  392.83   4.03  34.7  
    3     18.7  394.63   2.94  33.4  
    4     18.7  396.90   5.33  36.2  
    (506, 14)
    ------------------세부 정보--------------------
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 506 entries, 0 to 505
    Data columns (total 14 columns):
     #   Column   Non-Null Count  Dtype  
    ---  ------   --------------  -----  
     0   CRIM     506 non-null    float64
     1   ZN       506 non-null    float64
     2   INDUS    506 non-null    float64
     3   CHAS     506 non-null    float64
     4   NOX      506 non-null    float64
     5   RM       506 non-null    float64
     6   AGE      506 non-null    float64
     7   DIS      506 non-null    float64
     8   RAD      506 non-null    float64
     9   TAX      506 non-null    float64
     10  PTRATIO  506 non-null    float64
     11  B        506 non-null    float64
     12  LSTAT    506 non-null    float64
     13  MEDV     506 non-null    float64
    dtypes: float64(14)
    memory usage: 55.5 KB
    None
    ------------------통계 정보 --------------------
                 CRIM          ZN       INDUS        CHAS         NOX          RM  \
    count  506.000000  506.000000  506.000000  506.000000  506.000000  506.000000   
    mean     3.613524   11.363636   11.136779    0.069170    0.554695    6.284634   
    std      8.601545   23.322453    6.860353    0.253994    0.115878    0.702617   
    min      0.006320    0.000000    0.460000    0.000000    0.385000    3.561000   
    25%      0.082045    0.000000    5.190000    0.000000    0.449000    5.885500   
    50%      0.256510    0.000000    9.690000    0.000000    0.538000    6.208500   
    75%      3.677083   12.500000   18.100000    0.000000    0.624000    6.623500   
    max     88.976200  100.000000   27.740000    1.000000    0.871000    8.780000   
    
                  AGE         DIS         RAD         TAX     PTRATIO           B  \
    count  506.000000  506.000000  506.000000  506.000000  506.000000  506.000000   
    mean    68.574901    3.795043    9.549407  408.237154   18.455534  356.674032   
    std     28.148861    2.105710    8.707259  168.537116    2.164946   91.294864   
    min      2.900000    1.129600    1.000000  187.000000   12.600000    0.320000   
    25%     45.025000    2.100175    4.000000  279.000000   17.400000  375.377500   
    50%     77.500000    3.207450    5.000000  330.000000   19.050000  391.440000   
    75%     94.075000    5.188425   24.000000  666.000000   20.200000  396.225000   
    max    100.000000   12.126500   24.000000  711.000000   22.000000  396.900000   
    
                LSTAT        MEDV  
    count  506.000000  506.000000  
    mean    12.653063   22.532806  
    std      7.141062    9.197104  
    min      1.730000    5.000000  
    25%      6.950000   17.025000  
    50%     11.360000   21.200000  
    75%     16.955000   25.000000  
    max     37.970000   50.000000  
    22.532806324110677 21.2 84.58672359409856
    0      24.0
    1      21.6
    2      34.7
    3      33.4
    4      36.2
           ... 
    501    22.4
    502    20.6
    503    23.9
    504    22.0
    505    11.9
    Name: MEDV, Length: 506, dtype: float64
         AGE  MEDV
    0   65.2  24.0
    1   78.9  21.6
    2   61.1  34.7
    3   45.8  33.4
    4   54.2  36.2
    5   58.7  28.7
    6   66.6  22.9
    7   96.1  27.1
    8  100.0  16.5
    9   85.9  18.9



![png](https://tva1.sinaimg.cn/large/007S8ZIlgy1gijly2gmixj30a806wmwz.jpg)



```python
# -*- coding:utf-8 -*-
#Pandas 및 필요한 라이브러리 가져오기 
import pandas as pd
from pandas import DataFrame
import numpy as np

df = pd.DataFrame({
        'A':[11,21,31],
        'B':[12,22,32],
        'C':[13,23,33]},
        index=['ONE','TWO','THREE']
    )
print(df)
print('판다스 버전',pd.__version__)
print(pd.show_versions())

#DataFrame.rename(**kwargs)[source]
new_df = df.rename(columns={'A':'A01'},index={'ONE':'one'})
print(new_df)
new_df = df.rename(columns={'A':'a','C':'c'})
print(new_df)

#copy() 복사 
res_df = df.copy()
print('df: ',df)
print('-----------')
print(res_df) #res_df는 df의 주소를 갖고 있음 
r = res_df.rename(columns={'a':'aaa'},inplace=True)
print(r)
print('============')
print(df)
```

            A   B   C
    ONE    11  12  13
    TWO    21  22  23
    THREE  31  32  33
    판다스 버전 1.1.1
    
    INSTALLED VERSIONS
    ------------------
    commit           : f2ca0a2665b2d169c97de87b8e778dbed86aea07
    python           : 3.8.5.final.0
    python-bits      : 64
    OS               : Darwin
    OS-release       : 19.6.0
    Version          : Darwin Kernel Version 19.6.0: Thu Jun 18 20:49:00 PDT 2020; root:xnu-6153.141.1~1/RELEASE_X86_64
    machine          : x86_64
    processor        : i386
    byteorder        : little
    LC_ALL           : None
    LANG             : ko_KR.UTF-8
    LOCALE           : ko_KR.UTF-8
    
    pandas           : 1.1.1
    numpy            : 1.19.1
    pytz             : 2020.1
    dateutil         : 2.8.1
    pip              : 20.2.2
    setuptools       : 49.2.0
    Cython           : None
    pytest           : None
    hypothesis       : None
    sphinx           : None
    blosc            : None
    feather          : None
    xlsxwriter       : None
    lxml.etree       : None
    html5lib         : None
    pymysql          : None
    psycopg2         : None
    jinja2           : 2.11.2
    IPython          : 7.17.0
    pandas_datareader: None
    bs4              : 4.9.1
    bottleneck       : None
    fsspec           : None
    fastparquet      : None
    gcsfs            : None
    matplotlib       : 3.3.1
    numexpr          : 2.7.1
    odfpy            : None
    openpyxl         : None
    pandas_gbq       : None
    pyarrow          : None
    pytables         : None
    pyxlsb           : None
    s3fs             : None
    scipy            : 1.5.2
    sqlalchemy       : None
    tables           : None
    tabulate         : None
    xarray           : None
    xlrd             : None
    xlwt             : None
    numba            : None
    None
           A01   B   C
    one     11  12  13
    TWO     21  22  23
    THREE   31  32  33
            a   B   c
    ONE    11  12  13
    TWO    21  22  23
    THREE  31  32  33
    df:          A   B   C
    ONE    11  12  13
    TWO    21  22  23
    THREE  31  32  33
    -----------
            A   B   C
    ONE    11  12  13
    TWO    21  22  23
    THREE  31  32  33
    None
    ============
            A   B   C
    ONE    11  12  13
    TWO    21  22  23
    THREE  31  32  33


- rename() - index, DataFrame.add_prefix() - 열 , DataFrame.add_suffix() - 열


```python
df = pd.DataFrame({
    'A':[11,21,31],
    'B':[12,22,32],
    'C':[13,23,33]},
    index=['ONE','TWO','THREE']
)
print(df.rename(columns=str.lower, index=str.lower))
print(df.rename(columns=lambda s:s*3, index=lambda s:s+'$'))
print(df.add_prefix('X_'))       
print(df.add_suffix('_X'))        
```

            a   b   c
    one    11  12  13
    two    21  22  23
    three  31  32  33
            AAA  BBB  CCC
    ONE$     11   12   13
    TWO$     21   22   23
    THREE$   31   32   33
           X_A  X_B  X_C
    ONE     11   12   13
    TWO     21   22   23
    THREE   31   32   33
           A_X  B_X  C_X
    ONE     11   12   13
    TWO     21   22   23
    THREE   31   32   33


- 속성을  이용해서 프레임을 생성해보자


```python
df = pd.DataFrame({
    'A':[11,21,31],
    'B':[12,22,32],
    'C':[13,23,33]},
    index=['ONE','TWO','THREE']
) 
df.index=['ONE','TWO','THREE']
df.columns=['aaa','bbb','ccc']
print(df)
print('----------------------------------')
#Series([data, index, dtype, name, copy, ...]) 1차원 객체를 생성하자 
s = pd.Series([1,2,3],index=['a','b','c'])
print(s)
print(s.rename({'a':'abc','c':'def'}))
print(s.rename(str.upper))
```

- 데이터를 결손치 타입확인 및 자리수 체크 


```python
s = pd.Series([16666,2999,34444],dtype='f8') #float64, f8, np.float64
print(s,s.dtype)
print(s)
print(s.astype(str).str.len())
s02 = pd.Series([1.2,2,3,'abcd',pd.np.nan]) #결손치 pd.np.nan
print(s02)
print(s02.map(type))
```

    0    16666.0
    1     2999.0
    2    34444.0
    dtype: float64 float64
    0    16666.0
    1     2999.0
    2    34444.0
    dtype: float64
    0    7
    1    6
    2    7
    dtype: int64
    0     1.2
    1       2
    2       3
    3    abcd
    4     NaN
    dtype: object
    0    <class 'float'>
    1      <class 'int'>
    2      <class 'int'>
    3      <class 'str'>
    4    <class 'float'>
    dtype: object


    <ipython-input-48-1951db46266d>:5: FutureWarning: The pandas.np module is deprecated and will be removed from pandas in a future version. Import numpy directly instead
      s02 = pd.Series([1.2,2,3,'abcd',pd.np.nan]) #결손치 pd.np.nan



```python
s02 = pd.Series([1.2,2,3,'abcd',pd.np.nan],dtype=str) #결손치 pd.np.nan
print(s02)
print(s02.map(type))
print(s02.str.len())
```

    0     1.2
    1       2
    2       3
    3    abcd
    4     NaN
    dtype: object
    0      <class 'str'>
    1      <class 'str'>
    2      <class 'str'>
    3      <class 'str'>
    4    <class 'float'>
    dtype: object
    0    3.0
    1    1.0
    2    1.0
    3    4.0
    4    NaN
    dtype: float64


    <ipython-input-49-5f8053fc5bdd>:1: FutureWarning: The pandas.np module is deprecated and will be removed from pandas in a future version. Import numpy directly instead
      s02 = pd.Series([1.2,2,3,'abcd',pd.np.nan],dtype=str) #결손치 pd.np.nan



```python
#데이터 처리시 결손치는 중요함! 
s02 = pd.Series([1.2,2,3,'NULL', 'adbcd12345', pd.np.nan],dtype=str) #결손치 pd.np.nan
print(s02)
print(s02.map(type))
print(s02.str.len())
print('null 확인 : ', s02.isnull(),s02.isna()) #str로 변환하게 되면 NULL 키워드가 문자열 0으로 변환됨 
print(s02.dropna())
```

    0           1.2
    1             2
    2             3
    3          NULL
    4    adbcd12345
    5           NaN
    dtype: object
    0      <class 'str'>
    1      <class 'str'>
    2      <class 'str'>
    3      <class 'str'>
    4      <class 'str'>
    5    <class 'float'>
    dtype: object
    0     3.0
    1     1.0
    2     1.0
    3     4.0
    4    10.0
    5     NaN
    dtype: float64
    null 확인 :  0    False
    1    False
    2    False
    3    False
    4    False
    5     True
    dtype: bool 0    False
    1    False
    2    False
    3    False
    4    False
    5     True
    dtype: bool
    0           1.2
    1             2
    2             3
    3          NULL
    4    adbcd12345
    dtype: object


    <ipython-input-50-8c045e647b49>:2: FutureWarning: The pandas.np module is deprecated and will be removed from pandas in a future version. Import numpy directly instead
      s02 = pd.Series([1.2,2,3,'NULL', 'adbcd12345', pd.np.nan],dtype=str) #결손치 pd.np.nan


- 파일 입출력 


```python
df =pd.read_csv('~/playdata/workspace/python-work/PyProject/test-pandas/sample.csv')
print(df)
print(df['a'],type(df['a']))
    
#캐스팅 
s = df['a']
s_res = s.astype('float')
print(s_res)
#c열의 데이터를 object로 변
c = df['c']
c_res = c.astype('object')
print(c_res.map(type))
    
b_res = df['b'] #파일에서 read_csv()로 읽어들인 데이터는 df의 객체를 통해 클래스로 지정되어 리턴 
print(b_res.map(type))
    
#b열의 값의 타입을 바꾸어 보자 
b_res = df.astype({'b':float})
print(b_res)
print('b_res.dtypes => ',b_res.dtypes)
```

        a   b   c   d
    0  11  12  13  14
    1  21  22  23  24
    2  31  32  33  34
    0    11
    1    21
    2    31
    Name: a, dtype: int64 <class 'pandas.core.series.Series'>
    0    11.0
    1    21.0
    2    31.0
    Name: a, dtype: float64
    0    <class 'int'>
    1    <class 'int'>
    2    <class 'int'>
    Name: c, dtype: object
    0    <class 'int'>
    1    <class 'int'>
    2    <class 'int'>
    Name: b, dtype: object
        a     b   c   d
    0  11  12.0  13  14
    1  21  22.0  23  24
    2  31  32.0  33  34
    b_res.dtypes =>  a      int64
    b    float64
    c      int64
    d      int64
    dtype: object



```python
df =pd.read_csv('~/playdata/workspace/python-work/PyProject/test-pandas/sample02.csv')
print(df)
```

      Unnamed: 0  a    b      c  d
    0        ONE  1    1  100.0  x
    1        TWO  2   20    NaN  y
    2      THREE  3  300  300.0  z



```python
#index_col=0 추가 
df =pd.read_csv('~/playdata/workspace/python-work/PyProject/test-pandas/sample02.csv', index_col=0, dtype='object')
print(df.applymap(type))
print('-----------------------------')
print(df)
print(df.astype(str).applymap(type))
```

                       a              b                c              d
    ONE    <class 'str'>  <class 'str'>    <class 'str'>  <class 'str'>
    TWO    <class 'str'>  <class 'str'>  <class 'float'>  <class 'str'>
    THREE  <class 'str'>  <class 'str'>    <class 'str'>  <class 'str'>
    -----------------------------
           a    b    c  d
    ONE    1  001  100  x
    TWO    2  020  NaN  y
    THREE  3  300  300  z
                       a              b              c              d
    ONE    <class 'str'>  <class 'str'>  <class 'str'>  <class 'str'>
    TWO    <class 'str'>  <class 'str'>  <class 'str'>  <class 'str'>
    THREE  <class 'str'>  <class 'str'>  <class 'str'>  <class 'str'>


- dtype을 구체적으로 줘보자 


```python
df =pd.read_csv('~/playdata/workspace/python-work/PyProject/test-pandas/sample02.csv', index_col=0, dtype={'a':float,'b':str})
print(df)
print(df.dtypes)

```

             a    b      c  d
    ONE    1.0  001  100.0  x
    TWO    2.0  020    NaN  y
    THREE  3.0  300  300.0  z
    a    float64
    b     object
    c    float64
    d     object
    dtype: object


- dtype을 index로도 줄 수 있음


```python
df =pd.read_csv('~/playdata/workspace/python-work/PyProject/test-pandas/sample02.csv', index_col=0, dtype={1:float,2:str})
print(df)
print(df.dtypes)
# 당연 컬럼 간의 연산도 가능함 + * / // 다 됨
print(df['a']+df['c'])
#값 수정도 가능 
df.at['TWO','a']=100000
print(df)
```

             a    b      c  d
    ONE    1.0  001  100.0  x
    TWO    2.0  020    NaN  y
    THREE  3.0  300  300.0  z
    a    float64
    b     object
    c    float64
    d     object
    dtype: object
    ONE      101.0
    TWO        NaN
    THREE    303.0
    dtype: float64
                  a    b      c  d
    ONE         1.0  001  100.0  x
    TWO    100000.0  020    NaN  y
    THREE       3.0  300  300.0  z



- numexpr 설치 

```shell
$ pip install numexpr

```


```python
import numexpr

df = pd.read_csv('~/playdata/workspace/python-work/PyProject/test-pandas/sample03.csv')
print(df)
print('--------------------------')

# age 30이하인 모든 데이터를 추출 해보자 
print(df[df['age']< 30])
print('-----------query---------------')
print(df.query('age<30'))
print('------ 나이가 30보다 작지 않은 목록을 출력하자 ------')
print(df.query('not age<30'))
print('------ 포인트 점수가 60~70인 목록을 출력하자 ------')
print(df.query('60<= point <=70 '))
print('------ 포인트 점수가 60~70인 목록을 출력하자 ------')
print(df.query('60<= point <=70 '))
print(df.query('age < point/3'))
print('------ state가 NY인 것만 추출 ------')
print(df.query("state == 'NY'"))
print('------ state가 NY 또는 TX인 것만 추출 ------')
print(df.query("state in ['NY','TX']"))
print(df.query("state == ['NY','TX']"))
print(print(df.query("name.str.endswith('밥')",engine='python')))
df['name'].fillna('밥',inplace=True)
print(df)
```

       name  age state  point
    0   엘리스   24    NY     64
    1     밥   42    CA     92
    2  도미니까   18    CA     70
    3  데이비드   68    TX     70
    4    엘렌   24    CA     88
    5   플랭크   30    NY     57
    --------------------------
       name  age state  point
    0   엘리스   24    NY     64
    2  도미니까   18    CA     70
    4    엘렌   24    CA     88
    -----------query---------------
       name  age state  point
    0   엘리스   24    NY     64
    2  도미니까   18    CA     70
    4    엘렌   24    CA     88
    ------ 나이가 30보다 작지 않은 목록을 출력하자 ------
       name  age state  point
    1     밥   42    CA     92
    3  데이비드   68    TX     70
    5   플랭크   30    NY     57
    ------ 포인트 점수가 60~70인 목록을 출력하자 ------
       name  age state  point
    0   엘리스   24    NY     64
    2  도미니까   18    CA     70
    3  데이비드   68    TX     70
    ------ 포인트 점수가 60~70인 목록을 출력하자 ------
       name  age state  point
    0   엘리스   24    NY     64
    2  도미니까   18    CA     70
    3  데이비드   68    TX     70
       name  age state  point
    2  도미니까   18    CA     70
    4    엘렌   24    CA     88
    ------ state가 NY인 것만 추출 ------
      name  age state  point
    0  엘리스   24    NY     64
    5  플랭크   30    NY     57
    ------ state가 NY 또는 TX인 것만 추출 ------
       name  age state  point
    0   엘리스   24    NY     64
    3  데이비드   68    TX     70
    5   플랭크   30    NY     57
       name  age state  point
    0   엘리스   24    NY     64
    3  데이비드   68    TX     70
    5   플랭크   30    NY     57
      name  age state  point
    1    밥   42    CA     92
    None
       name  age state  point
    0   엘리스   24    NY     64
    1     밥   42    CA     92
    2  도미니까   18    CA     70
    3  데이비드   68    TX     70
    4    엘렌   24    CA     88
    5   플랭크   30    NY     57

