---
layout: single
title: "[Pandas] DataFrame 생성 및 확인"
last_modified_at: 2025-08-17
categories: ["파이썬"]
tags: ["Pandas"]
excerpt: "ICLR 2023"
use_math: true
toc: true
toc_sticky: true
---

## DataFrame 생성

### 1. 딕셔너리를 이용해 생성

```python
d = {'a':[1,2,3], 'b':[4,5,6], 'c':[7,8,9]}

df = pd.DataFrame(d)
```

### 2. 파일을 불러와 생성

```python
df = pd.read_csv('파일 경로')
```

## DataFrame 확인

### 1. 

```python
df.head(n)  # 앞에서부터 n개 행 출력 (default: n=5)
df.tail(n)  # 뒤에서부터 n개 행 출력 (default: n=5)
```

```python
df.column   # column name 출력
```

<details>
<summary><font color='blue'>실행 결과</font></summary>
<div markdown="1">

```python
Index(['date', 'city', 'avg_temp_c', 'rainfall_mm', 'humidity', 'weather_category'],
      dtype='object')
```

</div>
</details>
<br>

### 2. 요약 및 통계

```python
df.shape        # DataFrame의 행 개수와 열 개수를 tuple로 출력
```

```python
df.info()       # DataFrame 정보 출력
```

<details>
<summary><font color='blue'>실행 결과</font></summary>
<div markdown="1">

```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 21 entries, 0 to 20
Data columns (total 6 columns):
 #   Column            Non-Null Count  Dtype  
---  ------            --------------  -----  
 0   date              21 non-null     object 
 1   city              21 non-null     object 
 2   avg_temp_c        21 non-null     float64
 3   rainfall_mm       21 non-null     float64
 4   humidity          21 non-null     int64  
 5   weather_category  21 non-null     object 
dtypes: float64(2), int64(1), object(3)
memory usage: 1.1+ KB
```

- RangeIndex    : Index의 개수와 범위
- Non-Null Count: 각 column에서 결측치가 아닌 값의 개수
- Dtype         : 각 column의 데이터 타입

</div>
</details>
<br>

```python
df.describe()   # 숫자형 변수의 column에 대한 통계 출력
```

<details>
<summary><font color='blue'>실행 결과</font></summary>
<div markdown="1">

```python
       avg_temp_c  rainfall_mm   humidity
count   21.000000    21.000000  21.000000
mean    27.842857     7.395238  78.571429
std      1.067039     8.138579   6.910654
min     26.100000     0.000000  68.000000
25%     26.900000     0.800000  73.000000
50%     27.900000     4.100000  78.000000
75%     28.800000    12.500000  85.000000
max     29.500000    25.400000  90.000000
```

</div>
</details>
<br>

