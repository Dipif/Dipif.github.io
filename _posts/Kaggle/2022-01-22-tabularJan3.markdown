---
layout: post
read_time: true
show_date: true
title:  Kaggle tabular playground Jan - 3
date:   2022-1-22 01:40
categories: [Kaggle]
tags: [Kaggle]
author: 이대겸
github:  
mathjax: yes
---

지난 포스트에서 마무리하려 했는데, 우연찮게 이번 캐글 경진대회의 주제로 이 항목이 선택되어 다시 시도해보게 되었다.  
지난번의 실패를 교훈삼아 gdp에 대한 반영, 계절의 변화에 따른 주기를 삼각함수로 근사, 주말에 의한 효과 및 연휴에 의한 효과까지 
고려하는 대계획을 세웠으나, 오늘 주어진 7시간안에 이를 수행하는건 무리였었다.  
그럴때 번뜩이는 아이디어가 떠올랐다. 1년단위로 거의 반복되는데다가 주말만 맞춰주면 된다면 그냥 2018년의 데이터를 2019년으로 요일만 맞춰서 
옮겨주면 되지 않겠는가? 이를 바탕으로 작성한 코드이다.
~~~python

class Market :
    def __init__(self) :
        pass
    
    def move_apprx(self, x, y) :
        self.dgdp = {'Finland' : 0.973, 'Norway' : 0.92, 'Sweden' : 0.946}
        for c in x['country'].unique() :
            for s in x['store'].unique() :
                for p in x['product'].unique() :
                    a = x[(x['country'] == c) & (x['store'] == s) & 
                          (x['product'] == p) & (x['year'] == '2018')]['num_sold'].to_numpy(dtype = np.float64)
                    a = np.concatenate((a[:4], a[5:362], a[361:]), axis = None)
                    a *= self.dgdp[c]
                    y.loc[(y['country'] == c) & (y['store'] == s) & (y['product'] == p), 'num_sold'] = a
                    y[['row_id', 'num_sold']].to_csv('submission.csv', index = False)
        return y
                    
    def pre_processing(self, df) :
        df = df.drop(df[df['date'] == '2016-02-29'].index)
        df['year'] = df['date'].map(lambda x : x[:4])
        return df

md = Market()
x = pd.read_csv('../input/tabular-playground-series-jan-2022/train.csv')
y = pd.read_csv('../input/tabular-playground-series-jan-2022/test.csv')
x = md.pre_processing(x)
md.move_apprx(x, y)
~~~
gdp를 반영하기 위한 약간의 조정계수가 있을 뿐으로 지극히 단순한 이 방법을 사용해서 자그마치 5.8점, 등수로 상위 35%에 해당하는 결과를 얻었다. 
titanic을 할 때도 초반에 시도했던 여자와 15살 이하만 살리는 단순한 방법을 뛰어넘기가 매우 힘들었는데, 이 또한 마찬가지인 것 같다.  
한편 이 방법이 강력한 이유가 막 떠올랐는데, 이는 애초에 예측을 하는 문제에서 이미 관측된 자료인 gdp를 사용했기 때문인 것 같다. 
실제 문제라면 말이 안되는 일이지만 초심자를 위한 연습문제이니 만큼 discussion의 활용이나 domain에 대한 조사가 얼마나 중요한지 
깨우치게 하는 목적이 있다는 생각이 든다. 일단 이 competition은 잊 hyper parameter를 조정하는 것 만으로 5점을 뚫을수 있을 느낌이 드니 
남은 기간동안 심심풀이로 건드려봐야겠다. 진짜 마무리.