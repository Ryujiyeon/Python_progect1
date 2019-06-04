# 본인의 과제명 작성

학과 | 학번 | 성명
---- | ---- | ---- 
수학과|201711120 |류지연


## 프로젝트 개요
1. 데이터를 다루기 쉽도록 조작하기
	1)csv파일 읽기
	2)다루기 편하도록 열 이름 재설정
	3)유효하지 않은 데이터를 가진 행 삭제하기
	4)데이터 타입이 string으로 되어있는 것을 float로 바꿔주기
2. 각 년도 별로 수입과 지출의 차를 계산하여서 새로운 열에 추가해주기
3. 각 년도 별로 전년도 대비 성장률을 구해 이를 나타내는 표 다시 작성하기
4. 과정 3에서 각 년도 별로 가장 우수한 나라 5개를 뽑아 출력하고 그래프 그리기
	1)데이터 내림차순 정렬하기
	2)위의 5개 국가 추출하기
	3)matplot.pyplot 이용해서 그래프 그리기 
5. 나라이름을 입력받아 해당나라의 성장률을 출력하고 그래프 그리기
	1)나라를 입력받고 그 나라가 OECD국가인지, 유효한 데이터를 가지고 있는지 판단하기
	2)유효하다면 matplot.pyplot 이용해서 그래프 그리기
	3)'q‘를 입력받을 때까지 위의 과정 반복하기
  

## 사용한 공공데이터 
[데이터보기]http://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=DT_2KAAA15_OECD

## 소스
* [링크로 소스 내용 보기]https://github.com/Ryujiyeon/Python_progect1/blob/master/homework.py

* 코드 삽입
~~~python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt


#<STEP1: Manipulating the dataframe to be easy for treating>

#1-1.csv파일 읽어오기
df=pd.read_csv('travel_OECD1.csv').drop(0)

#1-2.이용하기 편리하도록 각 열의 이름을 바꿔준다.
#방법:df.rename(columns={원래 열의 이름:새로운 이름},inplace=True)
df.rename(columns={'관광수입 (100만달러)':'2014수입(100만달러)',
        '관광수입 (100만달러).1':'2015수입(100만달러)',
        '관광수입 (100만달러).2':'2016수입(100만달러)',
        '관광지출 (100만달러)':'2014지출(100만달러)',
        '관광지출 (100만달러).1':'2015지출(100만달러)',
        '관광지출 (100만달러).2':'2016지출(100만달러)'},inplace=True)

#1-3-1.단지 대륙을 알려주기 위한 행들을 삭제하기!
#이 행들만이 모든 열의 데이터값이 Nan이다->Deleting all rows (첫행의 값이 Nan인것)
#방법: df.dropna(subset=[열의 이름]) 해당열의 값이 Nan인 행을 모두 지운다.
df = df.dropna(subset=['2014수입(100만달러)'])

#나중에 input("나라")->OECD국가인지 아닌지 판단하기 위함.
CountryOECD=list(df['국가별'])   #OECD국가들의 리스트.
n= len(CountryOECD)

#행의 인덱스를 재정의해준다.
#앞 과정에서 대륙을 나타내는 행들을 삭제하여서 행의 인덱스가 중간중간 빠져있다.
#방법: df.index=바꾸고자 하는 인덱스수열
df.index=range(n)

#1-3-2.데이터에 -이 있는 나라(행)(즉, 데이터가 유효하지 않은 나라)는 모두 삭제하기
for i in range(n):
    if '-' in list(df.loc[i]):
        df=df.drop(i)   #행 삭제하기 방법: df.drop(해당인덱스)
#다시 한번 행의 인덱스를 재정의해준다.
df.index=range(len(df['국가별']))
#나중에input("나라")-> OECD국가 이지만 데이터가 유효하지 않은 국가임에 해당함을 보여주기 위함.
CorrectCountry=list(df['국가별'])

#1-4.지출의 데이터타입이 string으로 되어 있어 계산시에  오류가 나기에 float로 바꿔준다.
#방법: df[해당 열이름]=df[해당 열이름].astype(바꿀type)
df['2014지출(100만달러)'] = df['2014지출(100만달러)'].astype(float)
df['2015지출(100만달러)'] = df['2015지출(100만달러)'].astype(float)
df['2016지출(100만달러)'] = df['2016지출(100만달러)'].astype(float)
df['2016수입(100만달러)'] = df['2016수입(100만달러)'].astype(float)

print(df)

#**************************
#STEP2: Calculate the difference between import and export for each year.

d2014=df.iloc[:,[1,4]].diff(axis=1).iloc[:,-1]
d2015=df.iloc[:,[2,5]].diff(axis=1).iloc[:,-1]
d2016=df.iloc[:,[3,6]].diff(axis=1).iloc[:,-1]
df['수입-지출(2014)']=d2014
df['수입-지출(2015)']=d2015
df['수입-지출(2016)']=d2016

print("Dataframe with difference between import and export: \n",df)
print(df)

#**************************
#STEP3: Calculate the rate of growing
#성장률=(현재-과거)/과거*100
#방법: 각 column을 식에 넣으면 각 행에 대해서 계산해준다.
#dfgrade는 성장률을 보여주는 새로운  데이터 프레임이다.
dfgrade=pd.DataFrame()  #비어있는 dataframe 선언
dfgrade["국가"]=CorrectCountry
dfgrade['성장률(2014~2015)']=((d2015-d2014)/d2014)*100
dfgrade['성장률(2015~2016)']=((d2016-d2015)/d2015)*100
print("Dataframe of rate of growing for each year")
print(dfgrade)

#***************************
#STEP4: Showing best 5 countries!
#4-1.위의 dfgrade의 각 열을 내림차순으로 정렬시킨다.
#방법: df.sort_values(by='정렬을 원하고자 하는 열이름'ascending=False(내림차순) or True(오름차순)
Sort14_15=dfgrade.sort_values(by="성장률(2014~2015)",ascending=False)[["국가","성장률(2014~2015)"]]
Sort15_16=dfgrade.sort_values(by="성장률(2015~2016)",ascending=False)[["국가","성장률(2015~2016)"]]
#정렬시킨 열의 첫 5개의 행이 얻고자 한 데이터! head()함수를 이용해서 가져온다.
print("Best 5 of ""Rate of growth"" in 2014 ~ 2015:\n",Sort14_15.head(n=5),"\n")
print("Best 5 of ""Rate of growth"" in 2015 ~ 2016:\n",Sort15_16.head(n=5),"\n")

#By using matplotlib.pyplot
from matplotlib import font_manager, rc
font_name=font_manager.FontProperties(fname="c:/Windows/Fonts/malgun.ttf").get_name()
rc('font',family=font_name)

x= [0,1,2,3,4]
x14_15= Sort14_15['국가'].head(5)
plt.plot(x,Sort14_15['성장률(2014~2015)'].head(5),'bo-')
plt.xticks(x,x14_15)
plt.xlabel('나라')
plt.ylabel('성장률(%)')
plt.title("Best 5 in (2014~2015)")
plt.show()

x15_16=Sort15_16['국가'].head(5)
plt.plot(x,Sort15_16['성장률(2015~2016)'].head(5),'go-')
plt.xticks(x,x15_16)
plt.xlabel('나라')
plt.ylabel('성장률(%)')
plt.title("Best 5 in (2015~2016)")
plt.show()

#**********************************

#Step5: Input("원하는 나라")
while True:
    Country=input("What country do you want??\nIf you want to quit, Enter 'q'")
    if Country=='q':
        break
    elif Country not in CountryOECD:
        print("Not OECD Country!!!\nTry again~")
        continue
    elif Country not in CorrectCountry:
        print("It is OECD Country, But it does not have valid data!!!\nTry again~")
        continue
    else:
        print("Rate of growth from 2014 to 2015:")
        print(Sort14_15[Sort14_15['국가']==Country])
        print("Rate of growth from 2015 to 2016:")
        print(Sort15_16[Sort15_16['국가']==Country])
        x=[0,1]
        xx=['2014~2015','2015~2016']
        #dataframe에서 그래프를 그릴 때 y축에 오는 특정값을 뽑아내어야한다.
        y=[Sort14_15[Sort14_15['국가']==Country].reset_index().ix[0,2],Sort15_16[Sort15_16['국가']==Country].reset_index().ix[0,2]]
        plt.plot(x,y,'bo-')
        plt.xticks(x,xx)
        plt.title(Country+"의 성장률")
        plt.show()

        continue

~~~
