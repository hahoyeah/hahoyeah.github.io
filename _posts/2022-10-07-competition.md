---
layout: single
title:  "대회일지_파생변수"
---

# 2022.10.07

**대회를 하며 만든 파생변수**

나만의 인사이트를 도출하여 새로운 피쳐들을 추가해줬다.  
새로운 피쳐를 만드는 일은 정말 머리가 아팠다.  
창의력과 연관성을 함께 봐줘야 해서 언제나 제일 힘든 작업인 거 같다.  
그래도 새로운 피쳐를 만들었는데 그 피쳐가 예측력에 도움이 되었을 때의 행복감은 최고다ㅎㅎ

피쳐는 총 5개를 추가해줬다.  
그리고 요일과 년도 칼럼도 추가해줬다.

* 요일추가  
우선 날짜를 '년도-월-일' 이런 식인 칼럼(datetime)을 추가하여 이 칼럼을 이용하여 구했다.  
구한 요일을 수치화해줬다.

```python
train['datetime'] = train['datadate'].apply(lambda x: pd.to_datetime(str(x), format='%Y-%m-%d'))
train['week_day'] = train['datetime'].dt.day_name()

train.loc[train['week_day'] == 'Sunday', 'week_day'] = 0
train.loc[train['week_day'] == 'Monday', 'week_day'] = 1
train.loc[train['week_day'] == 'Tuesday', 'week_day'] = 2
train.loc[train['week_day'] == 'Wednesday', 'week_day'] = 3
train.loc[train['week_day'] == 'Thursday', 'week_day'] = 4
train.loc[train['week_day'] == 'Friday', 'week_day'] = 5
train.loc[train['week_day'] == 'Saturday', 'week_day'] = 6
```

* 년도 추가  
split을 해줘서 '-'를 기준으로 구분해줬다.  
구분한 후 가장 앞에 있는 인덱스(년도)를 [0]을 사용하여 꺼냈다.  
그리고 int를 사용하여 정수형으로 타입을 지정해줬다.

```python
train['datetime'] = train['datetime'].astype(str)
train['year'] = train['datetime'].apply(lambda x : x.split('-')[0]) #년
train['year'] = train['year'].astype('int')
```

* 새로운 피쳐 추가  
피쳐를 추가해줄 때 기존의 피쳐들과 타겟의 상관관계를 분석했다.  
타겟과의 상관관계가 높은 피쳐들을 우선적으로 새롭게 파생하려고 했다.  
피쳐들을 곱해주기도 하고 빼주기도 하고 더해주며 새로운 변수들을 만들어갔다.  
도중에 나누기를 사용하여 무한 값이 등장하기도 했다ㅠㅠ  
피쳐를 만들 때 두 개의 피쳐를 사용하기도 하고 4개의 피쳐를 이용해 하나의 피쳐를 만들기도 했다.  
```python
train['c'] = train['a'] - train['b'] #이런식으로 만들어갔다.
```

* 상관관계 분석 코드
```python
import seaborn as sns 

plt.figure(figsize=(25,20))
sns.heatmap(data = train.corr(method='pearson'), annot=True, fmt='.2f', linewidths=.5, cmap='Blues')
```

* feature importance  
feature importance를 이용해 새롭게 만든 피쳐들을 확인해 보니 꽤 좋은 결과가 나왔다.

```python
x = train[feature_cols].columns
y = model.feature_importances_

plt.figure(figsize=(25,40))

plt.xlabel('모델의 변수중요도')
plt.ylabel('변수명')

plt.barh(x,y)
plt.show()
```
 
새로 만든  5개의 피쳐 중 특히 두 번째와 세 번째의 결과가 좋았다.  
여러 개의 피쳐를 곱하고 빼주고 한 덕분인 거 같다. 뿌듯했다.

피쳐를 만들 때면 창의력이 길러지는 기분이다ㅎㅎ  
많은 고민을 했고 도움이 되는 피쳐를 만들어서 좋았다.  
더 좋은 피쳐들을 앞으로도 만들어나가고 싶다!
