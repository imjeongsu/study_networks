✅ 문제 : 간단한 서버 관리 스크립트 작성
🔧 요구사항
start: 포트 8000에서 http.server를 백그라운드로 실행 (nohup, 로그는 server.log)


stop: 실행 중인 프로세스를 찾아 종료


status: 프로세스가 실행 중인지 확인하여 출력


restart: 중지 후 다시 실행


🎯 실행 예시
$ ./webserver.sh start
서버가 백그라운드에서 시작되었습니다.

$ ./webserver.sh status
서버 실행 중입니다. PID: 13579

$ ./webserver.sh stop
서버가 종료되었습니다.

$ ./webserver.sh tail_log
… log message 확인

문제 모두 조건에 따라:
if [ "$1" == "start" ] 식으로 흐름 제어


변수 PORT, PID, LOGFILE 등을 정의해 구성 가능

## webserver.sh 내부 소스 스크립트
```shell
PID=$( ps aux | grep "http.server" | head -n 1 | awk '{print $2}' )
sv_sts=$( ps aux | grep "http" | wc -l )

if [ "$1" == "start" ]; then
        nohup python3 -m http.server 8000 --bind 0.0.0.0 > server.log &
        echo "서버가 백그라운드에서 시작되었습니다."
        
elif [ "$1" == "status" ] ; then
        if [ "$sv_sts" == "2" ]; then
                echo "서버 실행 중입니다. PID: "$PID" "
        else
                echo "실행 중인 서버가 없습니다."
        fi

elif [ "$1" == "restart" ]; then
        kill -9 "$PID"
        nohup python3 -m http.server 8000 --bind 0.0.0.0 > server.log &
        echo "서버가 백그라운드에서 재실행되었습니다"   
elif [ "$1" == "stop" ]; then
        kill -9 "$PID"
        echo "서버가 종료되었습니다."
else
        tail -f "$1"

fi


```

## 결과값
```shell
[im@192.168.0.31 ~/Downloads]$ source webserver.sh start
서버가 백그라운드에서 시작되었습니다.
nohup: ignoring input and redirecting stderr to stdout

[im@192.168.0.31 ~/Downloads]$ source webserver.sh status
서버 실행 중입니다. PID: 43426 


[im@192.168.0.31 ~/Downloads]$ source webserver.sh stop
서버가 종료되었습니다.
[1]+  Killed                  nohup python3 -m http.server 8000 --bind 0.0.0.0 > server.log
[im@192.168.0.31 ~/Downloads]$ source webserver.sh status
실행 중인 서버가 없습니다.

[im@192.168.0.31 ~/Downloads]$ source webserver.sh start
서버가 백그라운드에서 시작되었습니다.
nohup: ignoring input and redirecting stderr to stdout

[im@192.168.0.31 ~/Downloads]$ source webserver.sh restart
서버가 백그라운드에서 재실행되었습니다
nohup: ignoring input and redirecting stderr to stdout
[1]   Killed                  nohup python3 -m http.server 8000 --bind 0.0.0.0 > server.log

[im@192.168.0.31 ~/Downloads]$ source webserver.sh status
서버 실행 중입니다. PID: 43533 
```