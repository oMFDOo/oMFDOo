
### Client 구동법
1. requests 설치

<img src="../%20src/client_guide_0.png" style="width : 400px;">

 - pip install requests
 - Sever request를 위해 위의 명령어를 통해 requests를 설치합니다.

<br><br><br>

2. client 폴더의 [app.py](../client/app.py)에서 주석처리된 곳에 센서 데이터를 읽어오는 코드를 넣습니다.

<img src="../%20src/client_guide_1.png" style="width : 400px;">
<br><br><br>

3. 센서를 통해 읽어온 온도/습도/조도의 데이터를 각 변수에 넣어줍니다.

<img src="../%20src/client_guide_2.png" style="width : 400px;">

 - 테스트 코드에는 랜덤데이터가 들어가있습니다.
 - 간단한 통신 테스트를 원한다면 코드를 건드리지 않아도 됩니다.
<br><br><br>

4. 데이터를 전송합니다.

<img src="../%20src/client_guide_3.png" style="width : 400px;">

 - data 변수에 전송될 데이터를 json형태로 담아놓습니다.
 - 현재 raspberry에 post로 데이터를 보내는 예제 입니다. API가 확정된다면 추후 문서로 작성됩니다.
 - 올바르게 전송/수신된 경우 Sever State는 200,  Response Data는 ok가 회신됩니다.

ctrl+f5

<br><br><br><br><hr><br>
