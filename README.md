# BA-term-project
## 1. Data Preparing
#### - Loading files using read_csv
```python
d_temp= pd.read_csv('C:/Users/.../data/전국_일별기온.csv',parse_dates=['구분'], encoding='cp949')
d_rain = pd.read_csv('C:/Users/.../data/전국_일별강수량.csv',parse_dates=['구분'], encoding='cp949')
m_trade = pd.read_csv('C:/Users/.../data/월간_수출수입.csv', encoding='cp949',thousands = ',')
m_price = pd.read_csv('C:/Users/.../data/월간_소비자물가지수.csv', encoding='cp949',thousands = ',')
y_produce = pd.read_csv('C:/Users/.../data/연간_생산면적생산량.csv', encoding='cp949',thousands = ',')
```
#### - Combining daily 'cabbage price' data divided by year
```python
folder_lists = os.listdir('C:/Users/.../data/배추가격/배추가격_상')
d_price = pd.DataFrame()
for i in range(0,len(folder_lists)):
    if folder_lists[i].split('.')[1] == 'csv':
        file = 'C:/Users/.../data/배추가격/배추가격_상/' + folder_lists[i]
        df = pd.read_csv(file,parse_dates=['구분'], encoding='cp949',thousands = ',') 
        d_price = pd.concat([d_price, df])

```


#### - Changing col name '날짜' to '구분' and '평균' to '평균배추가격' 
```python
d_rain.rename(columns={'날짜':'구분'}, inplace = True)
d_price.rename(columns={'평균':'평균배추가격'}, inplace = True)
# merging to d2
d1 = pd.merge(d_price, d_temp, on = '구분', how = 'left')
d2 = pd.merge(d1,d_rain,on = '구분', how = 'left')
```
#### - Adding 'Year', 'Month', 'Day' colums and Droping '구분' column
```python
d2['Year'] = pd.DatetimeIndex(d2['구분']).year
d2['Month'] = pd.DatetimeIndex(d2['구분']).month
d2['Day'] = pd.DatetimeIndex(d2['구분']).day
d2 = d2.drop(['구분'], axis = 1)
```
#### - Changing y_produce colum names and column 'Year''s type to int
```python
y_produce.columns = ['Year','생산면적 (ha)','생산량 (톤)']
y_produce['Year']=y_produce['Year'].astype(int)

# merging to d2
d2=pd.merge(d2, y_produce, how='left')
```
#### - Making 'Year' and 'Month' column in m_price to merge
```python
m_price['시점']=m_price['시점'].astype(str)

m_price['Year'] = m_price.시점.str.split('.').str[0]
m_price['Month'] = m_price.시점.str.split('.').str[1]
m_price.loc[m_price['Month'] == '1', 'Month'] = '10'
m_price.drop('시점', axis=1, inplace=True)

m_price['Year']=m_price['Year'].astype(int)
m_price['Month']=m_price['Month'].astype(int)

# merging to d2
d2=pd.merge(d2,m_price, how='left')
```
#### - Editing m_trade
 ```python
 m_trade.dropna(inplace=True)

m_trade['년월']=m_trade['년월'].astype(str)

m_trade['Year'] = m_trade.년월.str.split('.').str[0]
m_trade['Month'] = m_trade.년월.str.split('.').str[1]

m_trade.loc[m_trade['Month'] == '1', 'Month'] = '10'
m_trade.drop('년월', axis=1, inplace=True)

m_trade['Year']=m_trade['Year'].astype(int)
m_trade['Month']=m_trade['Month'].astype(int)


####- Deleting error data in inports
m_trade.rename(columns={'수입액($1000)':'수입액'}, inplace = True)
m_trade.rename(columns={'수입량(kg)':'수입량'}, inplace = True)

m_trade['수입액']=m_trade['수입액'].astype(int)
m_trade['수입량']=m_trade['수입량'].astype(int)

# If the import amount is greater than zero but the imports is zero because of degree '$1000'
m_trade= m_trade.loc[(m_trade.수입액 != 0) | (m_trade.수입량 == 0)]


#일별 데이터, 월별 데이터 합치기
d2=pd.merge(d2,m_trade, how='left')
 ```
 #### - Handling Missing Values
 ```python
 d2.info()
 ```
 

## 2. EDA
### Data Source
#### 1) Cabbage price
#### 2) Daily temperature 
#####  - avg, min and max temperature
#### 3) Daily rainfall
#### 4) Monthly Import and Export data
#####  - exports, amount of exports, imports, amount of imports
#### 5) Consumer price
#### 6) Yearly Production per area
### Understanding Data

## 3. Modeling
#### StandardScaler()와 Lasso 사용한 이유 - using pipeline



