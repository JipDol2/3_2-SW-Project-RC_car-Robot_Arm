3학년 2학기 소프트웨어공학론 프로젝트
====================================

총인원 : 4명  
사용한 기계 : RC Car + Robot Arm  
사용한 언어와 도구 : (C/C++) + (Arduino) + (ADOxx) + (node.js server(web server))  
----------------------------------------------------------------------------
개발 환경과 역할(자세한 내용은 ppt 참고)

- Arduino : RC Car 와 Robot Arm을 제어하고 명령하는 역할  
- ADOxx : RC Car 와 Robot Arm에게 원하는 명령을 좀더 편리하게 내릴수 있게 도와주는 tool  
- node.js server : ADOxx tool의 전달하는 역할  

이 rapository에는 "보고서,발표 ppt,Arduino source,node.js,ADOxx script"가 들어있다.

**사용방법**

<미리 준비되어야 할 사항>  
\*- Node.js 설치,ADOxx 설치(사용법은 youtube 참고),RC Car 와 Robot Arm 의 Arduino 소스 완료(작동이 되는걸 확인),Bluetooth가 잘 작동되는지 확인\*

1. node.js server source 에서 11번째줄에 Bluetooth가 연결된 장비의 port number를 입력해 준다.  
    ex) var serialPort = new SerialPort('COM20',false);  
2. 만일 공유기를 사용하고 있다면 포트포워딩을 해주어야 된다.  
    ex) iptime 공유기를 사용하고 있다면 192.168.0.1 로 접속하여 포트포워딩 진행  
3. ADOxx script (.asc파일)의 23번째 줄의 주소를 "**외부 주소**"로 변경  
    ex) SET ip:("http://210.117.182.28:10000/")  
4. node.js server 와 Arduino 에서 pasing 진행  
ex)   
```
node.js server
```
```node.js
array = JSON.parse(req.body);
            for (var x in array)
            {
               //var command_index = 1;
               c = array[x];
               if(c[0] != '_') 
                  if( c[0] != '0' ) 
                     {
                        //command += (++command_index);
                        command+=c;
                     }
            }
            console.log(command);
            serialPort.write(command, function(err, results) {});
            console.log('t'); 
```
    Arduino code
```c
String response = parsing();
    for (int i = 0; i < response.length(); i++)
    {
        automobile_Direction = response[i];
        Control();
        if (automobile_Direction != 'F')
            delay(500);
        if (automobile_Direction != 'S')
            lt_mode_update();
    }
```    
Parsing 을 해주는 이유는 Server에서 넘겨주는 형식이 정해져 있어 이를 본인이 내린 명령어만 구분할 수 있게 뽑아내서 전달해줘야 하기 때문.

*주의사항*
node.js server에서 "name"이 ADOxx의 객체의 이름과 다르다면 실행이 전혀 되지 않는다 그러니 꼭 오타가 없는지 확인해라
```node.js
if (name == "Automobile")
      {
         console.log(JSON.parse(req.body));
         command = "";
         var automobile01 = {
            url: 'http://192.168.1.3:10001',
            method: 'GET',
            headers: headers,
            body: req.body
            
         }......
```

5. ./node 서버이름  를 입력하여 서버를 키고 블루투스가 잘 연결이 되었는지 확인

6. ADOxx 에서 시나리오(어떤 명령을 줄 것인지)를 작성 후 ADoxx script(.asc)을 긁어서 붙여줌 그 후 실행

7.작동되는 것을 확인하면 끝 
