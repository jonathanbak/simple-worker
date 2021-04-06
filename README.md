# simple-worker
간단한 큐처리 데몬

## 설치
simple-worker 파일을 다운로드 받은후 테스트
```bash
$ wget https://github.com/jonathanbak/simple-worker/raw/main/simple-worker && chmod 755 simple-worker

$ simple-worker -max_workers=1 -max_queue_size=1200 -port=8087 -work_path=${WORKSPACE}
```

### 데몬으로 실행
```bash
$ nohup simple-worker -max_workers=1 -max_queue_size=1200 -port=8087 -work_path=${WORKSPACE} >> logfile 2>&1&

$ tail -f logfile
```


### 옵션 설명
  - max_workers [1] : 동시 처리할 쓰레드 수, 처리순서가 무조건 지켜져야 한다면 1로 셋팅, 동시에 병렬로 처리하시려면 원하는 숫자만큼 입력 (최대권장수 : cpu 코어수 * 2)
  - max_queue_size [1200] : 큐 크기, 큐작업이 밀린다고 가정했을시 최대 수용 가능 크기
  - port [8087] : 원하는 포트 선정
  - work_path [simple-worker실행위치]: 실행할 스크립트 위치
  
## 호출
```bash
$ curl -XGET http://localhost:8087/command/{실행할스크립트명}[/{파라미터1}/{파라미터2}/{파라미터3}...]
```
이렇게 호출하면 실제 실행되는 파일은 아래와 같다
```bash
$ ${WORKSPACE}/{실행할스크립트명} {파라미터1} {파라미터2} {파라미터3} ...
```

## 스크립트 파일 호출(실행) 예제
```bash
$ cat > test.sh << EOF
PARAM1=\$1
echo "PARAM1 : \${PARAM1}"
EOF

$ curl -XGET http://localhost:8087/command/test.sh/test1234

$ tail logfile
2021/03/18 08:46:22 [../test.sh test1234]
2021/03/18 08:46:22 REQ : 166d45fa5902567b - /command/test.sh/test1234
2021/03/18 08:46:22 worker1: started 1616024782833342075
2021/03/18 08:46:22 PARAM1 : test1234
2021/03/18 08:46:22 worker1: completed 1616024782833342075!
```

