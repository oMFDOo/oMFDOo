# 최적 환경 제어를 위한 LSTM 기반 시간 단위 환경 변화량 예측 시스템
LSTM을 기반으로 하여 온도, 습도, 기압을 예측해줍니다.

### 데이터 흐름도
<img src="./%20src/network.png" style="width : 1000px;">

## 목차
 <br>
 
 ### [1. 파일 구조](#파일-구조) <br>
 
 ### [2. Client](#client) <br>
 
 ### [3. Sever](#sever) <br>
 
 ### [4. REST API](#REST-API) <br>

 ### [5. LSTM](#LSTM) <br>



<br><br><br><hr><br>

## 파일 구조
```
├─AI
│  ├─data
│  ├─dataSet
│  └─modelT
│      └─variables
├─client
│  ├─.vscode
│  └─__pycache__
└─project
    ├─.vscode
    ├─static
    │  ├─js
    │  ├─scss
    │  │  ├─components
    │  │  ├─sections
    │  │  └─variables
    │  └─src
    │      └─img
    └─templates
```
 - AI : anaconda로 개발한 LSTM 기반 학습 모델
   - dataSet : 학습을 위한 데이터 모음
   - LSTM.py : dataSet에 있는 csv파일을 이용해 학습하는 모델
   - LSTMGetDB.py : DB에 있는 데이터를 이용해 학습하는 모델
   - model_(t/h/i)_N : 온도/습도/조도를 N시간 간격 데이터로 학습한 모델(1 <= N <= 12)
   
 - client : API 테스트를 위한 클라이언트
   - app.py : 클라이언트 테스트 코드
   
 - project : Flask API서버
   - app.py : 서버 API 코드
   - templates : 선택 시간에 대한 온도/습도/기압에 대한 예측을 보여주는 웹페이지 index.html 존재
   - static : 웹을 꾸미기 위한 js, scss, src
   
<br><br><br>

## Client
 > 서버로 데이터를 요청합니다.

 클라이언트는 Web, Python, Unity 세 가지로 구성되어 있습니다. <br>
 각 클라이언트의 역할은 다음과 같습니다.
 
 <span>
 
 <img src="./%20src/unity.PNG" style="width : 400px;">
 <img src="./%20src/client_demo_2.png" style="width : 200px;">
 <img src="./%20src/client_demo_3.png" style="width : 200px;">

 </span>

 
 |대상|기능|정의 장소|
 |:--:|:-------|:----:|
 |Unity|실시간 데이터 수신 <br>희망 환경 값 설정 <br>현재 환경에 대한 제어값 추천 <br>현재 환경에 대한 미래 예측| [/Unity](https://github.com/etri-summer-Intern/Unity)|
 |Web|희망 시간을 입력하면 해당 시간에 대한 예측된 온도/습도/기압을 보여줍니다.| [monitoring/project/templates/](https://github.com/etri-summer-Intern/monitoring/tree/main/project/templates) |
 |Python|각 API에 대해 테스트 할 수 있습니다.| [monitoring/client/](https://github.com/etri-summer-Intern/monitoring/tree/main/client) |
 
 - python 클라이언트 구동법은 [여기](https://github.com/etri-summer-Intern/monitoring/blob/main/doc/client.md)에서 확인하실 수 있습니다.

<br><br><br>

## Sever
 > 클라이언트의 요청을 받고 반환해줍니다.

서버는 Flask를 이용해 구동되고 있습니다. <br>
모든 서버와 클라이언트는 공유기의 유선 랜에 연결되어 동작중입니다.
|대상|IP/Port|목적|버전|담당|
|:--:|:-------|:----:|:---:|:--:|
|Flask Web Server| 192.168.0.2:5000 | 웹 페이지 동작 및 API 동작 | iMac (21.5-inch, Late 2012) |
|python | 192.168.0.3 | API 동작 테스트 | windows 10 |
|MariaDB| 192.168.0.5:3306 | 실시간 센서 데이터 저장(학습 및 실시간 데이터 전송에 이용) |  |
|Unity| 192.168.0.4 | 환경제어를 위한 클라이언트 | |

 - Flask 웹 서버 구동법은 [여기](https://github.com/etri-summer-Intern/monitoring/blob/main/doc/server.md)에서 확인하실 수 있습니다.

<br><br><br>


## REST API
 > 서버로 데이터를 요청하거나 수신합니다.

|타입|URI|요청 데이터|수신 데이터|목적|
|:--:|:--:|:----|:----|:------|
|Post|/raspberry|{ "temperature" : 현재 온도, "humidity" : 현재 습도, "illuminance" : 현재 조도} | "ok" | 라즈베리 센서의 실시간 데이터 전송용 |
|Post|/getValue| X | {"dataTime" : 수신 시간, "temperature" : 현재 온도, "humidity" : 현재 습도, "illuminance" : 현재 조도}  | DB의 제일 최신 데이터 1개 반환 |
|Post|/getRandom| X | {"temperature" : 랜덤 온도, "humidity" : 랜덤 습도, "illuminance" : 랜덤 조도}  | 테스트를 위해 랜덤 온도/습도/조도 반환 |
|Post|/getValue| {"dataTime" : 수신 시간, "temperature" : 현재 온도, "humidity" : 현재 습도, "illuminance" : 현재 조도}  | "ok" | 전송한 데이터를 DB에 저장 |
|Post|/getValue ForLSTM| X |{ "dataTime" : \[DB에서 가져온 시간 리스트\], "temperature": \[DB에서 가져온 온도\], "humidity": \[DB에서 가져온 습도\], "illuminance" : \[DB에서 가져온 조도\]} | LSTM 모델 예측을 위한 201개의 데이터를 최신순으로 반환 |
|Post|/getAll IntervalValue| {"iter" : 시간 간격, "len" : 가져오고 싶은 개수(0이면 모든 데이터)} |{ "dataTime" : \[DB에서 가져온 시간 리스트\], "temperature": \[DB에서 가져온 온도\], "humidity": \[DB에서 가져온 습도\], "illuminance" : \[DB에서 가져온 조도\]} | 원하는 시간 간격으로 DB의 모든 데이터 반환 |
|Post|/getInterval ValueForLSTM| {"year": 연, "month" : 월, "day" : 일, "hour" : 시, "min" : 분, "sec" : 초, "iter" : 시간 간격}|{ "dataTime" : \[DB에서 가져온 시간 리스트\], "temperature": \[DB에서 가져온 온도\], "humidity": \[DB에서 가져온 습도\], "illuminance" : \[DB에서 가져온 조도\]} | 원하는 시간 이전부터 201개의 데이터 반환. |
|Post|/getPrediction| {"iter" : n시간(1이면 1시간 후 미래 예측)} |{"dataTime" : 예측 기반 데이터의 시간, "temperature" : 예측 온도, "humidity" : 예측 습도, "illuminance" : 예측 조도} | 학습된 모델을 통해 희망하는 미래 시간의 온도/습도/조도에 대해 예측 |
|Post|/setEnv|{ "temperature" : 희망 온도, "humidity" : 희망 습도, "illuminance" : 희망 조도, "errorT" : 온도 허용 오차, "errorH" : 습도 허용 오차} | "ok" | 희망 환경(온도/습도/조도)과 해당 환경의 허용오차(온도/습도)를 설정. 조도는 켜짐/꺼짐으로 구분되어 오차 없음. |
|Post|/getControl| x | { "controlT": 더운 정도, "controlH": 습한 정도, "controlI": 조명 꺼짐/켜짐, "stateT": 온도 제어값, "stateH": 습도 제어값, "stateI": 조도 제어값, } | 현재 환경에 대한 제어값 반환, 값의 의미는 아래 문서 참조 |

 - 정의된 RESTAPI는 [여기](https://github.com/etri-summer-Intern/monitoring/blob/main/doc/restAPI.md)에서 확인하실 수 있습니다.
 

<br><br><br><hr><br><br><br>

## LSTM
 > 시계열 예측을 통해 희망시간의 온도/습도/조도를 예측합니다.

<img src="https://i.imgur.com/jKodJ1u.png" style="width : 500px;">

 - 순환신경망의 구조를 띄며 RNN의 장기 의존성을 극복한 모델이다. <br>
 - 망각, 입력 게이트와 셀 상태를 두어 새로운 정보에 대한 검열이 이루어지며 학습한다. <br>
 - 긴 시간의 의존 문제에 대해 수행하기 위해 제작되어, 문자열 분석 및 시계열 데이터 예측에 많이 이용된다. 
 
<br><br><br>



## 학습 데이터
 > [기상청, 기상자료 개방포털 종관기상관측(ASOS) 자료](https://data.kma.go.kr/data/grnd/selectAsosRltmList.do?pgmNo=36)

<span>

<img src="./%20src/data_flow.png" style="width : 400px;">
<img src="./%20src/db_data.png" style="width : 400px;">

</span>

 - 2012년부터 2022년까지의 88,382개의 데이터를 이용해 학습
 - 온도, 습도, 기압 (조도 데이터는 미측정 수가 많았음.) 데이터 이용
 - 위와 같은 형태로 DB에 저장해서 사용 중. (num값은 auto_increment)
 - 시계열 데이터와 계절성 특징을 담고 있기에 LSTM을 이용해 예측 <br> <br> <br>
 
 
### 모델 학습 과정 
 |1시간 후를 예측하는 모델 제작|3시간 후를 예측하는 모델 제작|
 |:--:|:--:|
 | <img src="./%20src/1hour_Model.png" style="width : 350px;"> | <img src="./%20src/3hour_Model.png" style="width : 350px;"> |
 | - 1시간 간격을 가진 데이터로 학습 <br> - 학습된 모델에 200개의 1시간 간격 데이터로 예측 결과 도출 | - 1시간 간격을 가진 데이터로 학습 <br> - 학습된 모델에 200개의 3시간 간격 데이터로 예측 결과 도출 |
 
 <br> <br> <br>
 
### Q. 왜 200개의 데이터로 예측하는가?

 - LSTM은 학습 시 살펴볼 과거 데이터 수를 정하게된다. 현재 코드에서는 200으로 지정했다. <br>
 - 따라서 예측을 위해서는 200개의 데이터가 주어져야한다.  <br>
 - `LSTM.py` `LSTMGetDB.py` 파일의 `univariate_past_history` 변수가 해당값을 의미한다. <br>
 - `univariate_past_history`값을 변경하면 모델을 불러오는 코드들도 변화해야 한다. <br>
 - \server의 `app.py` 파일의 getPrediction 함수와 result 함수, `LoadModel.py`의 내용을 바꾸어주어야 한다. <br>
 - 예를들어, `univariate_past_history` 값을 50으로 변경해서 학습한 모델이 있다면, 기존 200으로 작성된 내용은 50, 201로 작성된 내용은 51로 바꾸어주면 된다. <br> <br> <br>

### Q. 구현된 API는 왜 202개를 반환하는가?
![image](https://user-images.githubusercontent.com/50069569/186797161-3a6ed3a8-a49a-4f86-b66a-5ab14458798f.png) <br>

 - 현재 구현된 DB 데이터를 반환하는 API는 데이터를 202개를 반환한다. <br>
 - 200개는 예측을 위해서 반환하고, 201번째 데이터는 모델이 예측한 값과 실제 값의 차이를 확인하는 용도이다. <br>
 - 202번째 데이터는 단순히 Out of Index 방지용이다. <br>
 - Error = abs(201번째 데이터 - 모델의 예측값)
 
  <br> <br> <br>
 
### Q. 학습데이터와 예측 데이터에 시간 간격은 왜 두었는가?
<img src="./%20src/performanceTable.png" style="width : 600px;">

 - **학습** : [1시간 간격 데이터] -> 모델 => 1시간 특징 학습 모델
   - **예측** : [1시간, 2시간, 4시간, 8시간 간격 데이터] 1시간 학습모델
   - **결과** : 시간 간격이 길어질 수록 예측도가 떨어진다. (오차 값이 커진다.)
 
  <br>
  
 - **학습** : [1시간, 2시간, 4시간, 8시간 간격 데이터] -> 모델 => 1시간, 2시간, 4시간, 8시간 특징 학습 모델
   - **예측** : [1시간]  ->  1시간 학습모델, [1시간]  ->  2시간 학습모델, [1시간]  ->  4시간 학습모델, [1시간]  ->  4시간 학습모델
   - **결과** : 예측 데이터의 시간 간격과 동일한 데이터로 학습한 모델에서 가장 좋은 성능을 보였다.

 => 결론 : **(학습 시 데이터의 시간 간격) == (예측 시 데이터의 시간 간격)** 일 때 가장 효율이 좋다.
  
 
  <br> <br> <br>
 
### Q. 성능은 어떤가?
<img src="./%20src/performance.png" style="width : 600px;">

오른쪽 그래프는 예측 모델에 따른 예측 데이터와 실제 데이터를 비교한 그래프입나다. <br>
위의 세 그래프는 1시간 간격의 데이터를, 아래 3개의 그래프는 8시간 간격의 데이터를 학습한 경우를 보입니다. <br>
1시간 간격의 모델은 온도를 기준으로 최대 오차 값이 0.8에 미쳤지만 8시간 모델은 1에서 6까지의 값을 보였습니다. <br>
시간 간격이 넓은 데이터로 먼 미래를 예측하고자 할 수록 오차 값이 증가하는 양상을 보입니다. <br>

  <br> <br> <br>

<img src="./%20src/predictionFlow.png" style="width : 400px;">
<img src="./%20src/LSTM.png" style="width : 400px;">


 <br> <br> <br> <br> <br>

## 팀원
| 팀원 | 담당 | github |
|:---:|:---|:--:|
| 남태민 | - Unity GUI 제작 <br> - 환경 제어문 작성 <br> - 센서 데이터 입력 | [Namtaemin](https://github.com/Namtaemin) |
| 황진주 | - LSTM 모델 작성 <br> - API 서버 구축 <br> - Web GUI 제작 <br> - Database 관리 | [oMFDOo](https://github.com/oMFDOo) |
