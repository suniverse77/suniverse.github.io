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

```
df.head(n)  # 앞에서부터 n개 행 출력 (default: n=5)
df.tail(n)  # 뒤에서부터 n개 행 출력 (default: n=5)
```

