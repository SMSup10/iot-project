# iot-project
창의공학 iot 실습 프로젝트

![KakaoTalk_20221204_234652674](https://user-images.githubusercontent.com/75311640/205497341-753b4fa0-65fe-4075-b5ca-07231bac731e.jpg)

# 아두이노 코드
void setup(){ 
Serial.begin(9600); //시리얼 시작 
pinMode(13, OUTPUT); //13번 핀을 사용하겠다.
}

double th(int v) {

double t;

t = log(((10240000/v) - 10000));

t = 1 /(0.001129148 + (0.000234125*t) + (0.0000000876741*t*t*t));

t = t - 273.15; // 화씨를 섭씨로 바꾸어줌

return t;

}

int dly;

void loop(){

int a=analogRead(A0); //서미스터 온도센서로 온도를 측정

Serial.println(th(a));  //시리얼을 통해 변환된 온도값을 서버로 전달

if(Serial.available()>0){

  dly = (Serial.readString()).toInt(); //클라이언트에서 보낸 값을 서버로 시리얼로 보낸다. 
  
}

digitalWrite(13, HIGH); // Led 켜짐

delay(dly); // dly 변수에 들어오는 값만큼 쉬겠다.

digitalWrite(13, LOW); // Led 켜짐

delay(dly); // dly 변수에 들어오는 값만큼 쉬겠다.

}
# 프로세싱 코드
import processing.serial.; // 시리얼을 사용하겠다는 의미의 코드

import processing.net.;


Serial p; // 시리얼로 아두이노와 연결

Server s; // 서버로 클라이언트와 연결

Client c; // 앱인벤터를 뜻함

void setup() {

s = new Server(this, 12345); // 포트 12345로 서버를 만듭니다.

p = new Serial(this, "COM3", 9600); // 포트번호 3번에 있는 아두이노와 연결합니다

}

String msg;

void draw() {

c = s.available();

if (c!=null) { //클라이언트가 존재할때

String m = c.readString(); // 클라이언트에서 보낸 문자열을 받아라.

if (m.indexOf("PUT")==0) { // 깜빡이는 주기를 의미

int n = m.indexOf("\r\n\r\n")+4;

m = m.substring(n);

m += '\n';

print(m); // 서버화면에 출력합니다

p.write(m); // 아두이노에 시리얼로 값을 전달

}

else if (m.indexOf("GET")==0) { // 온도값

 if (msg!=null) {

c.write("HTTP/1.1 200 OK\r\n"); // POST 규격

c.write("Content-length: " + msg.length() + " \r\n\r\n"); // 데이터 길이

c.write(msg); // 클라이언트에 값을 전달

}

}

}

if (p.available()>0) { // 시리얼이 통신이 될떄

String m = p.readStringUntil('\n'); // 엔터가 나올때까지 읽습니다

if (m!=null) {

msg = m;

print(m);

}

}

}

# 앱인벤터 코드


![코드 ㅇ앱](https://user-images.githubusercontent.com/75311640/205496759-4929d34e-4c9a-423c-857f-70325abd45fa.PNG)

//서버에서 텍스트가 전달되어지면 라벨1의 텍스트에 받은 온도값으로 넣겠다.

//서버로 textbox1의 값을 전달 서버에서 값을 받아온다.


