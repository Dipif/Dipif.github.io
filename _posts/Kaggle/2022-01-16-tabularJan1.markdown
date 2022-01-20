---
layout: post
read_time: true
show_date: true
title:  Kaggle tabular playground Jan - 1
date:   2022-1-16 01:45
categories: [Kaggle]
tags: [Kaggle]
author: 이대겸
github:  
mathjax: yes
---

며칠 전에 titanic으로 kaggle을 입문한 뒤 조금 더 연습해보기 위해 쉬운 competition에 참가하였다. 
Kaggle에서 정기적으로 열고있는 입문자를 위한 tabular playground 라는 competition이다. 
그 과정을 블로그에 정리해 올려볼 생각이다. 

![data_info](/assets/img/posts/kgl_tabular_1/kgl_tabular1_info.png)

먼저 데이터의 간단한 성질들을 살펴 보았다. 2015년 1월 1일부터 2018년 12월 31일까지 여러나라, 여러가게, 여러품목들이 얼마나 팔렸는지 기록되있다. 
결측치가 없는 깔끔한 데이터인 것도 알 수 있었다. 참고로 이 competition의 목표는 주어진 데이터를 이용하여 2019년동안 각 물건들이 얼마나 팔릴 것인지 
예측하는 것이다.

![data_group](/assets/img/posts/kgl_tabular_1/kgl_tabular1_group.png)

각 물건들의 판매량이 시간에 따라 어떻게 변화했는지 알아보기 위해 그룹별로 묶어주었다. 이 때 index를 date로, column을 적당히 조합하여 만들었는데 
이 방법이 그다지 좋지 않은 느낌이 든다. 일단 column에 접근하는 것이 매우 비직관적이다. 

![eda1](/assets/img/posts/kgl_tabular_1/kgl_tabular1_eda1.png)

그래도 그룹화한 테이블을 이용하여 date를 x축으로 하는 (Sweden, KaggleMart, Kaggle Sticker)의 그래프를 그려보았다. 
주기적으로 이상치가 관측되는 걸 알 수 있고, 이전에 date의 정렬이 이상했던 데이터가 있었던 점을 고려해 date를 살펴보았으나 문제가 없었다. 
그러나 일단 이상치를 누그러트려 보기 위해 10-MA를 적용하여 보았다.

![eda2](/assets/img/posts/kgl_tabular_1/kgl_tabular1_eda2.png)

그래프가 보기 더 편해진 것과 더불어, 몇 가지 특징이 더 드러났다. 우선 연말에 발생하는 이상치 뿐만 아니라 여름 쯤에도 특이한 모양의 매출이 발생하고 있다. 
또한 약간이나마 그래프가 우상향으로 가고 있다. 이런 점들을 고려해 몇 가지 시도를 해 볼 것이다.
1. 그래프 전체에 대해 linear regression을 써 볼 수 있다. 물론 눈에 띄게 보이는 편차높은 값 들로 인해 좋은 결과를 기대할 순 없겠으나 
다른 방법을 적용할 때 비교할 수 있는 최저한의 기준으로 삼을 수 있을 것이다.
2. 주기적으로 반복되는 변화를 반영하기 위해서 월/일이 같은 날끼리 모아서 linear regression을 시도해 볼 수 있다. 예를 들면 2015/1/1, 2016/1/1, 
2017/1/1 의 값 세개를 이용하여 2018/1/1의 값을 예측하는 것이다. 한편 이 방법의 문제로 regression당 사용되는 데이터가 너무 적어 
알 수 없는 사건으로 인해 딱 하루의 값이 크게 변동하는 것 만으로 예측이 크게 어긋날 수 있다는 것이다.
3. 연도별 평균 변화율 $\alpha$를 사용할 수 있다. $\alpha$를 일단 구하면 2015, 2016, 2017 3년간의 데이터 중에서 중위값이나 평균을 고르고 $\alpha^2$을
곱한다던가 하는 기법 또한 적용할 수 있다.


