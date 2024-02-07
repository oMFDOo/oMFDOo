
### 설정된 REST API

![image](https://user-images.githubusercontent.com/50069569/186548473-390db3a9-e0b1-4efa-b178-c3441594ae06.png)


## 1. (POST) http://192.168.0.2:5000/raspberry
 - 용도 : 라즈베리 파이에서 수신한 센서 값을 전송합니다.
 - 전송 데이터 타입
   data ={ <br>
     "temperature": 전송할 온도, <br>
     "humidity": 전송할 습도, <br>
     "illuminance" : 전송할 조도 <br>
   }
 - 수신 값 : "ok"
 
<br>

## 2. (POST) http://192.168.0.2:5000/getValue
 - 용도 : 최신 온도, 습도, 조도를 반환합니다.
 - 전송 데이터 형식 : "" >> 데이터 내용 없음
 - 수신 값 :   <br>
   { <br>
     "dataTime" : 수신 시간, <br>
     "temperature": 현재 온도, <br>
     "humidity": 현재 습도, <br>
     "illuminance" : 현재 조도 <br>
   }
   
<br>

## 3. (POST) http://192.168.0.2:5000/getRandom
 - 용도 : 랜덤 온도, 습도, 조도를 반환합니다.
 - 전송 데이터 형식 : "" >> 데이터 내용 없음
 - 수신 값 :   <br>
   { <br>
     "temperature": 랜덤 온도, <br>
     "humidity": 랜덤 습도, <br>
     "illuminance" : 랜덤 조도 <br>
   }
 
<br>
 
## 4. (POST) http://192.168.0.2:5000/csvToDB
 - 용도 : 전송받은 데이터를 db에 저장합니다.
 - 전송 데이터 타입
   data ={ <br>
     "dateTime" : 시간, <br>
     "temperature": 온도, <br>
     "humidity": 습도, <br>
     "illuminance" : 조도 <br>
   }
 - 수신 값 : "ok"
 
<br>
 
## 5. (POST) http://192.168.0.2:5000/getValueForLSTM
 - 용도 : LSTM 예측을 위한 최근 데이터 201개를 반환합니다.
 - 전송 데이터 형식 : "" >> 데이터 내용 없음
 - 수신 값 :   <br>
   { <br>
     "temperature": 랜덤 온도, <br>
     "humidity": 랜덤 습도, <br>
     "illuminance" : 랜덤 조도 <br>
   }
   
<br>
 
## 6. (POST) http://192.168.0.2:5000/getAllIntervalValue
 - 용도 : LSTM 모델 학습을 위해 모든 데이터를 희망하는 시간 간격으로 반환합니다. len이 0이면 모든 데이터를 가져옵니다.
 - 전송 데이터 형식 :
   data ={ <br>
     "iter" : 시간 간격, <br>
     "len" : 가져오고 싶은 개수 <br>
   }
 - 수신 값 :   <br>
   { <br>
     "dataTime" : DB에서 가져온 시간 <br>
     "temperature": DB에서 가져온 온도, <br>
     "humidity": DB에서 가져온 습도, <br>
     "illuminance" : DB에서 가져온 조도 <br>
   }
      
<br>
 
## 7. (POST) http://192.168.0.2:5000/getIntervalValueForLSTM
 - 용도 : LSTM 예측을 위한 데이터를 희망하는 시간 간격으로 201개를 반환합니다.
 - 전송 데이터 형식 : <br>
   data ={ <br>
   "year": 연, <br>
   "month" : 월, <br>
   "day" : 일, <br>
   "hour" : 시, <br>
   "min" : 분, <br>
   "sec" : 초, <br>
   "iter" : 시간 간격 <br>
   } <br>
 - 수신 값 :   <br>
   { <br>
     "dataTime" : DB에서 가져온 시간 <br>
     "temperature": DB에서 가져온 온도, <br>
     "humidity": DB에서 가져온 습도, <br>
     "illuminance" : DB에서 가져온 조도 <br>
   }


<br>
 
## 8. (POST) http://192.168.0.2:5000/getPrediction
 - 용도 : 학습된 모델에서 희망하는 미래시간(1입력시 1시간 뒤의 미래)에 대해 예측
 - 전송 데이터 형식 :
   data ={ <br>
     "iter" : 시간 간격 <br>
   } <br>
 - 수신 값 :   <br>
   { <br>
     "dataTime" : 해당 시간 <br>
     "temperature": 예측 온도, <br>
     "humidity": 예측 습도, <br>
     "illuminance" : 예측 조도 <br>
   } <br>


<br>
 
## 9. (POST) http://192.168.0.2:5000/setEnv
 - 용도 : 환경에 대한 희망 환경(온도/습도/조도)와 허용 오차(온도/습도) 설정
 - 전송 데이터 형식 :
   data ={ <br>
     "temperature": 예측 온도, <br>
     "humidity": 예측 습도, <br>
     "illuminance" : 예측 조도,  <br>
     "errorT" : 허용 온도 오차, <br>
     "errorH" : 허용 습도 오차 <br>
   } <br>
 - 수신 값 :   <br>
   { <br>
     "temperature": 설정된 온도, <br>
     "humidity": 설정된 습도, <br>
     "illuminance" : 설정된 조도,  <br>
     "errorT" : 설정된 온도 오차, <br>
     "errorH" : 설정된 습도 오차 <br>
   }



<br>
 
## 10. (POST) http://192.168.0.2:5000/getControl
 - 용도 : 환경에 대한 희망 환경(온도/습도/조도)와 허용 오차(온도/습도) 설정
 - 전송 데이터 형식 :
   data = "" >> 데이터 내용 없음
 - 수신 값 :  
    제어값(High, Over, Middle, Under, Low) = (값의 정도) <br>
    온도 : HT, OT, MT, UT, LT = (1.0, 0.7, 0.5, 0.3, 0.1) <br>
    습도 : HH, OH, MH, UH, LH  = (1.0, 0.7, 0.5, 0.3, 0.1) <br>
    조도 : HI, LI = (1.0, 0.1) <br>
   { <br>
     "controlT": 더운 정도, <br>
     "controlH": 습한 정도, <br>
     "controlI": 조명 꺼짐/켜짐, <br>
     "stateT": 온도 제어값, <br>
     "stateH": 습도 제어값, <br>
     "stateI": 조도 제어값, <br>
   }

