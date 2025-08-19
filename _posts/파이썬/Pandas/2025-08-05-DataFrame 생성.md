---
layout: single
title: "[Pandas] DataFrame 생성"
last_modified_at: 2025-08-05
categories: ["파이썬"]
tags: ["Pandas"]
excerpt: ""
use_math: true
toc: true
toc_sticky: true
---

## 1. 딕셔너리를 이용해 생성

```python
d = {'a':[1,2,3], 'b':[4,5,6], 'c':[7,8,9]}

df = pd.DataFrame(d)
```

## 2. 파일을 불러와 생성

```python
df = pd.read_csv('파일 경로')
```
