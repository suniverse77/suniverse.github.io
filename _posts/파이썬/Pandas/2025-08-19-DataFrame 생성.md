---
layout: single
title: "[Pandas] DataFrame 생성 및 확인"
last_modified_at: 2025-08-19
categories: ["파이썬"]
tags: ["Pandas"]
excerpt: "ICLR 2023"
use_math: true
toc: true
toc_sticky: true
---

## DataFrame 생성

### 1. 딕셔너리를 이용해 생성

```
d = {'a':[1,2,3], 'b':[4,5,6], 'c':[7,8,9]}

df = pd.DataFrame(d)
```

### 2. 파일을 불러와 생성

```
df = pd.read_csv('파일 경로')
```

## DataFrame 확인

### 1. 

```
df.head(n)  # 앞에서부터 n개 행 출력 (default: n=5)
df.tail(n)  # 뒤에서부터 n개 행 출력 (default: n=5)
```

### 2. 요약 및 통계

```
df.info()   # 데이터 출력
```

<details>
<summary><font color='blue'>실행 결과</font></summary>
<div markdown="1">

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 8228 entries, 0 to 8227
Data columns (total 11 columns):
 #   Column       Non-Null Count  Dtype  
---  ------       --------------  -----  
 0   Unnamed: 0   8228 non-null   int64  
 1   class        8228 non-null   object 
 2   sex          8228 non-null   object 
 3   age          8228 non-null   float64
 4   service      8228 non-null   int64  
 5   stop         8228 non-null   object 
 6   npay         8228 non-null   object 
 7   avg_bill     8228 non-null   float64
 8   A_bill       8228 non-null   float64
 9   B_bill       8228 non-null   float64
 10  termination  8228 non-null   object 
dtypes: float64(4), int64(2), object(5)
memory usage: 707.2+ KB
```

- RangeIndex    : Index의 개수와 범위
- Non-Null Count: 각 column에서 결측치가 아닌 값의 개수
- Dtype         : 각 column의 데이터 타입

</div>
</details>

```
df.describe()   # 숫자형 변수에 대한 통계 출력
```

<details>
<summary><font color='blue'>실행 결과</font></summary>
<div markdown="1">

```
Unnamed: 0	age	service	avg_bill	A_bill	B_bill
count	8228.000000	8228.000000	8228.000000	8228.000000	8228.000000	8228.000000
mean	4114.500000	47.879801	1.226544	10356.605452	1764.512253	5630.461347
std	2375.363341	19.255090	0.800793	8394.568408	2599.854946	4380.126308
min	1.000000	4.000000	0.000000	299.900100	0.000000	0.000000
25%	2057.750000	32.000000	1.000000	4725.601635	386.149950	2385.362475
50%	4114.500000	49.000000	1.000000	9396.623330	1665.000000	5404.765400
75%	6171.250000	61.000000	1.000000	14322.583350	2325.562500	8434.983300
max	8228.000000	104.000000	14.000000	144739.686600	131581.533300	65836.558700

        Unnamed: 0          age      service       avg_bill         A_bill  \
count  8228.000000  8228.000000  8228.000000    8228.000000    8228.000000   
mean   4114.500000    47.879801     1.226544   10356.605452    1764.512253   
std    2375.363341    19.255090     0.800793    8394.568408    2599.854946   
min       1.000000     4.000000     0.000000     299.900100       0.000000   
25%    2057.750000    32.000000     1.000000    4725.601635     386.149950   
50%    4114.500000    49.000000     1.000000    9396.623330    1665.000000   
75%    6171.250000    61.000000     1.000000   14322.583350    2325.562500   
max    8228.000000   104.000000    14.000000  144739.686600  131581.533300   

             B_bill  
count   8228.000000  
mean    5630.461347  
std     4380.126308  
min        0.000000  
25%     2385.362475  
50%     5404.765400  
75%     8434.983300  
max    65836.558700  
```

- RangeIndex    : Index의 개수와 범위
- Non-Null Count: 각 column에서 결측치가 아닌 값의 개수
- Dtype         : 각 column의 데이터 타입

</div>
</details>