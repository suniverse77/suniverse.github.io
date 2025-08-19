---
layout: single
title: "[Pandas] DataFrame 병합"
last_modified_at: 2025-08-20
categories: ["파이썬"]
tags: ["Pandas"]
excerpt: "concat / merge"
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
df.info()   # 데이터
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

RangeIndex    : Index의 개수와 범위
Non-Null Count: 각 column에서 결측치가 아닌 값의 개수
Dtype         : 각 column의 데이터 타입

</div>
</details>

