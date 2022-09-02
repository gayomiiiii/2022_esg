# ESG 데이터 전처리 및 결측치 채우기

### RAW데이터 전처리 

#### 데이터 불러오기
```PYTHON
df_esg = pd.read_csv('C:\\Users\\최가영\\Desktop\\2908_(송부용) 한국산업단지공단_ESG관련 입주업체 데이터 추출결과_송부용 (자동 저장됨).csv', encoding='cp949')
df_esg

설명
1. ()내 데이터 경로 설정을 통해 원본데이터를 불러온다
2. 원본데이터 내 기업명은 개인정보보호로 인해 임의의 값으로 치환함

```

#### 열삭제
```PYTHON
df_esg.drop(['열이름'], axis=1, inplace=True)
df_esg.columns

설명
1. []내 열이름을 적음으로써 필요없는 열 삭제함
```

#### 결측치 제거
```PYTHON
df_esg = df_esg.dropna(subset = ['기업규모'], axis = 0)
df_esg = df_esg.dropna(subset = ['기업상태'], axis = 0)
df_esg

설명
1. []내 열이름을 적음으로써 해당 열의 결측치를 행기준으로 제거함
```

#### 기업규모 전처리
```PYTHON

idx_nm_1 = df_esg[(df_esg['기업규모'] == '판단제외')].index
df_esg = df_esg.drop(idx_nm_1)

df_esg.loc[df_esg['기업규모'] == '보호대상중견기업', '기업규모'] = '중견기업'

df_esg.loc[df_esg['기업규모'] == '중기업', '기업규모'] = '중소기업'
df_esg.loc[df_esg['기업규모'] == '한시성중소기업', '기업규모'] = '중소기업'
df_esg.loc[df_esg['기업규모'] == '소기업', '기업규모'] = '중소기업'
df_esg.loc[df_esg['기업규모'] == '소상공인', '기업규모'] = '중소기업'

설명
1. 기업규모 내 판단제외 제거하기
2. 기업규모를 대기업/중견기업/중소기업으로 나누기
```

#### 기업상태 전처리
```PYTHON

idx_nm_1 = df_esg[(df_esg['기업상태'] == '폐업') | (df_esg['기업상태'] == '청산/해산') | (df_esg['기업상태'] == '휴업') | (df_esg['기업상태'] == '피흡수합병')].index
df_esg = df_esg.drop(idx_nm_1)

설명
1. 기업상태가 정상인것만 남기기
```

#### 매출액 결측치 채우기 
##### (분기 매출액으로 년도 매출액 결측치 채우기)
```PYTHON

df_esg['mean2020']=df_esg.iloc[:,20:24].mean(axis=1)
df_esg['매출액_2020'] = np.where(pd.notnull(df_esg['매출액_2020']) == True, df_esg['매출액_2020'], df_esg['mean2020'])

df_esg['mean2021']=df_esg.iloc[:,24:28].mean(axis=1)
df_esg['매출액_2021'] = np.where(pd.notnull(df_esg['매출액_2021']) == True, df_esg['매출액_2021'], df_esg['mean2021'])

설명
1. 20, 21년도의 분기별 매출액 평균값 구하기
2. 분기별 평균값으로 20, 21년도 매출액 결측치 채우기
```
#### 파일 저장
```PYTHON

df_esg.to_csv("esg_final.csv", encoding= 'cp949', index = False)

설명
1. 전처리 파일 csv로 저장하기
```
