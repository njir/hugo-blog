+++
title = "Docker에서 EOS 실행하기"
description = "2018-01-29일을 기준으로 Docker에서 eos 실행해보기"
tags = [
    "eos",
    "blockchain",
]
date = "2018-01-29"
menu = "main"
+++


# Docker에서 EOS 실행하기

> **요약:** 2018-01-29일 현재, eos 프로젝트 그대로 clone하면 Docker에서 [빌드 에러](https://github.com/EOSIO/eos/issues/1146) 발생 
<br>
> [PR #1184](https://github.com/EOSIO/eos/pull/1184), [PR #1218](https://github.com/EOSIO/eos/pull/1218) 참고하여 DockerFile 및 config.ini 수정해야 함

[EOS](https://github.com/EOSIO/eos)를 Docker 환경에서 실행해보기 위헤 [eos/Docker](https://github.com/EOSIO/eos/tree/master/Docker) 페이지를 보며 따라하고 있다. 우선 eos를 실행하기 위한 기본 Docker setting은 17.05 버전 이상, 8GB 이상의 메모리로 설정해야 한다.

- Docker setting
[![Docker Memory](https://user-images.githubusercontent.com/7614353/35480584-9b6fce84-0454-11e8-8a2c-00f3a47791b2.png)](https://user-images.githubusercontent.com/7614353/35480584-9b6fce84-0454-11e8-8a2c-00f3a47791b2.png) 

## EOS Image 빌드
Docker를 업데이트하고 기본 셋팅을 바꿨으면 eos Docker 이미지를 받아보자.

```
git clone https://github.com/EOSIO/eos.git --recursive
cd eos/Docker
docker build . -t eosio/eos
```

위 스크립트를 실행하면 Docker 관련 에러가 나온다. eos docker 이미지를 받으려면 커맨드 상에서 로그인한 상태여야 한다. Docker에 로그인해주자.

```
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
```

로그인 후, ```docker build . -t eosio/eos```를 다시 실행했다.
참고로 eos의 경우, Docker 이미지를 받고 세팅하는 데 오래 걸린다(약 50분). 긴 시간이 흐른 뒤에 다시 에러 메세지가 나타났다.

```
Step 20/27 : COPY --from=builder /opt/eos/bin /opt/eos/bin COPY failed: stat /var/lib/docker/overlay2/d66370d8a0140a0596ce407855eb13790db1a051570b4771bad485381c8853be/merged/opt/eos/bin: no such file or directory
```

Docker 설정을 하면서 뭔가 파일을 못찾아서 나온 에러였다. 아마 경로가 잘못되어 있었나? eos github를 보니 이미 issue에 올라온 [에러](https://github.com/EOSIO/eos/issues/1146)였다. 해결책은 DockerFile을 수정하는 것. Docker 폴더 내에 DockFile을 열고 아래와 같이 수정했다.

```diff
// DockerFile

RUN git clone -b master --depth 1 https://github.com/EOSIO/eos.git --recursive \
    && cd eos \
    && cmake -H. -B"/tmp/build" -GNinja -DCMAKE_BUILD_TYPE=Release -DWASM_LLVM_CONFIG=/opt/wasm/bin/llvm-config -DCMAKE_CXX_COMPILER=clang++ \
       -DCMAKE_C_COMPILER=clang -DCMAKE_INSTALL_PREFIX=/opt/eos  -DSecp256k1_ROOT_DIR=/usr/local \
-    && cmake --build /tmp/build --target install
-    
+    && cmake --build /tmp/build --target install \
+    && cd /tmp/build && cmake -DCMAKE_INSTALL_PREFIX=/opt/eos -P cmake_install.cmake


FROM ubuntu:16.04
```

다시 eos docker 이미지를 받아보자. ```docker build . -t eosio/eos```를 실행하자.
[![image-success](https://user-images.githubusercontent.com/7614353/35480858-31c4235c-045b-11e8-91ec-576f60aa0701.png)](https://user-images.githubusercontent.com/7614353/35480858-31c4235c-045b-11e8-91ec-576f60aa0701.png)

됐다. 드디어 image가 받아졌다. 이제 다운 받은 eos image를 실행해보자. 
해당 커맨드는 eos에 소개되어 있는 커맨드다.

```
$ docker run --name eosiod -p 8888:8888 -p 9876:9876 -t eosio/eos start_eosiod.sh arg1 arg2
```

위 커맨드를 실행하면 다음과 같은 에러가 나온다.
역시나 에러가 안나오면 이상하지. 이 세상엔 쉬운게 없다. 한번에 되길 바라는 것은 큰 욕심이다.
[![error](https://user-images.githubusercontent.com/7614353/35480856-291282a8-045b-11e8-9fc7-a5624d6c9da9.png)](https://user-images.githubusercontent.com/7614353/35480856-291282a8-045b-11e8-9fc7-a5624d6c9da9.png)

Docker 이미지에서 ```/opt/eos/bin/data-dir/genesis.json``` 파일이 없다는 에러다. 실제로 eos Docker 컨테이너로 접속하여 확인해보니 해당 폴더 내에 아무 파일도 없었다. ```/``` 경로에 관련 파일들이 다 있긴 했는데 뭔가 직접 옮기고 싶지 않고, 스크립트로 해결하고 싶어서 무시했다.

```bash
$ docker run -i -t eosio/eos /bin/bash
root@e1a820beab12:/#
root@e1a820beab12:/# ls
bin   config.ini  dev  genesis.json  lib    media  opt   root  sbin  sys  usr
boot  contracts   etc  home          lib64  mnt    proc  run   srv   tmp  var
root@e1a820beab12:/# cd opt/eos/bin/data-dir/
root@e1a820beab12:/opt/eos/bin/data-dir# ls
root@e1a820beab12:/opt/eos/bin/data-dir#
```

eos github에서 Docker 관련 에러를 찾아보니 비슷한 [issue](https://github.com/EOSIO/eos/issues/1219)와 [PR #1218](https://github.com/EOSIO/eos/pull/1218)이 올라와있었다. 해당 PR대로 DockerFile과 config.ini 파일을 수정했다.

```diff
// config.ini

# File to read Genesis State from
# genesis-json =
- genesis-json = "/opt/eos/bin/data-dir/genesis.json"
+ genesis-json = "/opt/eosio/bin/data-dir/genesis.json"

// DockerFile

FROM ubuntu:16.04
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install openssl && rm -rf /var/lib/apt/lists/*
COPY --from=builder /usr/local/lib/* /usr/local/lib/
- COPY --from=builder /opt/eosio/bin /opt/eosio/bin
+ COPY --from=builder /tmp/build/install/bin /opt/eosio/bin
COPY --from=builder /tmp/build/contracts /contracts
```

해당 PR대로 수정하고, 다시 docker build 시도했더니 이젠 docker가 난리다. 방금 받은 docker image의 container가 실행 중이란다. docker container부터 삭제해주고, 이미 받은 image도 삭제해버렸다.

```
// 현재 실행 중인 docker container 확인
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
941ec0585029        eosio/eos           "start_eosiod.sh arg…"   21 seconds ago      Exited (0) 23 seconds ago                       eosiod

// container 삭제
$ docker rm 941ec0585029

// docker 이미지 확인
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
eosio/eos           latest              b23b51a35625        About a minute ago   241MB
<none>              <none>              d1794d48d291        About a minute ago   2.92GB
ubuntu              16.04               0458a4468cbc        2 days ago           112MB

// docker image 삭제
$ docker rmi b23b51a35625 d1794d48d291
```

이제 실행 중인 eos container도 없고 image도 없다. 해야할 일은 [PR #1218](https://github.com/EOSIO/eos/pull/1218)처럼 수정하여 다시 eos docker 이미지를 받는 것.

```
// 여러번 다운 받아보는 eos docker image
$ docker build . -t eosio/eos

// 이미지를 받은 후 다시 docker container 실행
$ docker run --name eosiod -p 8888:8888 -p 9876:9876 -t eosio/eos start_eosiod.sh arg1 arg2
```

역시나 같은 에러다. 그놈의 genesis.json 파일이 뭔지 계속 못찾는다. README 대로 arg도 바꿔보고 별짓 다해봤지만 안된다.

```
$ docker run --name eosiod -p 8888:8888 -p 9876:9876 -t eosio/eos start_eosiod.sh --genesis-json /opt/eosio/bin/data-dir/genesis.json
$ docker run --name eosiod -p 8888:8888 -p 9876:9876 -t eosio/eos start_eosiod.sh --genesis-json /opt/eos/bin/data-dir/genesis.json
```

## DockerFile, config.ini 수정

대충 보니까 /opt/eos를 /opt/eosio로 네이밍하면서 실제 코드랑 docker 쪽이 싱크가 안맞나보다. DockerFile은 자세히 모르지만 [PR #1184](https://github.com/EOSIO/eos/pull/1184)의 코드에서 INSTALL_PREFIX 경로를 살짝 바꿔보고,  [PR #1218](https://github.com/EOSIO/eos/pull/1218)처럼 config.ini도 바꿔보았다.

```diff
diff --git a/Docker/Dockerfile b/Docker/Dockerfile
index 89ea2b22..d2f0973e 100644
--- a/Docker/Dockerfile
+++ b/Docker/Dockerfile
@@ -73,9 +73,9 @@ RUN git clone --depth 1 -b releases/stable git://github.com/mongodb/mongo-cxx-dr
 RUN git clone -b master --depth 1 https://github.com/EOSIO/eos.git --recursive \
     && cd eos \
     && cmake -H. -B"/tmp/build" -GNinja -DCMAKE_BUILD_TYPE=Release -DWASM_LLVM_CONFIG=/opt/wasm/bin/llvm-config -DCMAKE_CXX_COMPILER=clang++ \
-       -DCMAKE_C_COMPILER=clang -DCMAKE_INSTALL_PREFIX=/opt/eos  -DSecp256k1_ROOT_DIR=/usr/local \
-    && cmake --build /tmp/build --target install
-
+       -DCMAKE_C_COMPILER=clang -DCMAKE_INSTALL_PREFIX=/opt/eosio  -DSecp256k1_ROOT_DIR=/usr/local \
+    && cmake --build /tmp/build --target install \
+       && cd /tmp/build && cmake -DCMAKE_INSTALL_PREFIX=/opt/eosio -P cmake_install.cmake


 FROM ubuntu:16.04
diff --git a/Docker/config.ini b/Docker/config.ini
index 6401c549..11e51c14 100644
--- a/Docker/config.ini
+++ b/Docker/config.ini
@@ -1,6 +1,6 @@
 # File to read Genesis State from
 # genesis-json =
-genesis-json = "/opt/eos/bin/data-dir/genesis.json"
+genesis-json = "/opt/eosio/bin/data-dir/genesis.json"

 # the location of the block log (absolute path or relative to application data dir)
 block-log-dir = "blocks"
(END)
```

모든 eos docker 컨테이너 지우고 image도 지운 후, 위 설정대로 docker 빌드했더니 빌드는 잘된다. 이제 eos에 소개된 커맨드로 실행해보니 역시나 안된다. 

```
$ docker run --name eosiod -p 8888:8888 -p 9876:9876 -t eosio/eos start_eosiod.sh arg1 arg2
1584162ms thread-0   chain_plugin.cpp:109          plugin_initialize    ] initializing chain plugin
1584163ms thread-0   http_plugin.cpp:141           plugin_initialize    ] host: 0.0.0.0 port: 8888
1584163ms thread-0   http_plugin.cpp:144           plugin_initialize    ] configured http to listen on 0.0.0.0:8888
1584163ms thread-0   net_plugin.cpp:2376           plugin_initialize    ] Initialize net plugin
1584163ms thread-0   net_plugin.cpp:2396           plugin_initialize    ] Setting net_plugin logging level to info
1584163ms thread-0   net_plugin.cpp:2422           plugin_initialize    ] host: 0.0.0.0 port: 9876
1584164ms thread-0   net_plugin.cpp:2498           plugin_initialize    ] my node_id is efe7237ad945ddd96a8d73b93d1a3eedc9d33b88e42496a8bc4bfde9a7cd66a9
1584164ms thread-0   main.cpp:85                   main                 ] eosiod version 37312a4b
1584164ms thread-0   main.cpp:89                   main                 ] 10 assert_exception: Assert Exception
fc::exists( my->genesis_file ): unable to find genesis file '/opt/eos/bin/data-dir/genesis.json', check --genesis-json argument
    {"f":"/opt/eos/bin/data-dir/genesis.json"}
    thread-0  chain_plugin.cpp:189 plugin_startup

    {"my->genesis_file.generic_string()":"/opt/eos/bin/data-dir/genesis.json"}
    thread-0  chain_plugin.cpp:210 plugin_startup
```

```--genesis-json``` 옵션에 ```/opt/eosio/bin/data-dir``` 주고 다시 해보자

```
$ docker run --name eosiod -p 8888:8888 -p 9876:9876 -t eosio/eos start_eosiod.sh --genesis-json /opt/eosio/bin/data-dir/genesis.json

1771061ms thread-0   chain_plugin.cpp:109          plugin_initialize    ] initializing chain plugin
1771061ms thread-0   http_plugin.cpp:141           plugin_initialize    ] host: 0.0.0.0 port: 8888
1771062ms thread-0   http_plugin.cpp:144           plugin_initialize    ] configured http to listen on 0.0.0.0:8888
1771062ms thread-0   net_plugin.cpp:2376           plugin_initialize    ] Initialize net plugin
1771062ms thread-0   net_plugin.cpp:2396           plugin_initialize    ] Setting net_plugin logging level to info
1771062ms thread-0   net_plugin.cpp:2422           plugin_initialize    ] host: 0.0.0.0 port: 9876
1771063ms thread-0   net_plugin.cpp:2498           plugin_initialize    ] my node_id is f1d610309ae23a08ae333ca7b560aefa29043c5b900bb2b9799236ee941d9558
1771063ms thread-0   main.cpp:85                   main                 ] eosiod version 37312a4b
1771066ms thread-0   block_log.cpp:92              open                 ] Opening block log at /opt/eosio/bin/data-dir/blocks/blocks.log
1771095ms thread-0   chain_controller.cpp:1087     operator()           ] applying genesis transaction
1771096ms thread-0   chain_controller.cpp:1100     operator()           ] done applying genesis transaction
1771096ms thread-0   chain_plugin.cpp:201          plugin_startup       ] starting chain in read/write mode
1771096ms thread-0   chain_plugin.cpp:206          plugin_startup       ] Blockchain started; head block is #0, genesis timestamp is 2017-03-30T12:00:00.000
1771096ms thread-0   producer_plugin.cpp:152       plugin_startup       ] producer plugin:  plugin_startup() begin
1771096ms thread-0   producer_plugin.cpp:157       plugin_startup       ] Launching block production for 21 producers.

*******************************
*                             *
*   ------ NEW CHAIN ------   *
*   -  Welcome to EOSIO!  -   *
*   -----------------------   *
*                             *
*******************************
```

드디어 성공. 거의 이틀 삽질해서 Docker에 eos를 실행했다.
<br>이제 로컬 환경에서 아래 커맨드를 입력하면 실행중인 eos 체인 정보를 볼 수 있다.

```
$ curl http://127.0.0.1:8888/v1/chain/get_info
{"server_version":"37312a4b","head_block_num":337,"last_irreversible_block_num":257,"head_block_id":"00000151edbabd1fdcd7e09cdc5c9c7198f76b59df1213b359fc5eeb8e44c6c8","head_block_time":"2018-01-29T08:32:19","head_block_producer":"inito","recent_slots":"1111111111111111111111111111111111111111111111111111111111111111","participation_rate":"1.00000000000000000"}
```

## Docker Compose 사용하기
이제 거의 다 온거 같다. 문서에 소개되어 있는 스크립트를 실행해봤다.
```
$ docker-compose up
  Recreating docker_eosiod_1 ... done
  
  ...
  
  eosiod_1   | 1208309ms thread-0   net_plugin.cpp:2376           plugin_initialize    ] Initialize net plugin
  walletd_1  | 1209367ms thread-0   http_plugin.cpp:144           plugin_initialize    ] configured http to listen on 127.0.0.1:8888
  eosiod_1   | 1208309ms thread-0   net_plugin.cpp:2396           plugin_initialize    ] Setting net_plugin logging level to info
  eosiod_1   | 1208309ms thread-0   net_plugin.cpp:2422           plugin_initialize    ] host: 0.0.0.0 port: 9876
  walletd_1  | 1209368ms thread-0   http_plugin.cpp:156           plugin_startup       ] start processing http thread
  eosiod_1   | 1208311ms thread-0   net_plugin.cpp:2498           plugin_initialize    ] my node_id is a352950487baa060934d82d5d61dd8d1d22b4de20be4c01f39738774214123fb
  walletd_1  | 1209368ms thread-0   http_plugin.cpp:213           plugin_startup       ] start listening for http requests
  eosiod_1   | 1208311ms thread-0   main.cpp:85                   main                 ] eosiod version 37312a4b
  walletd_1  | 1209368ms thread-0   http_plugin.cpp:218           plugin_startup       ] http io service exit
  eosiod_1   | 1208312ms thread-0   main.cpp:89                   main                 ] 10 assert_exception: Assert Exception
  eosiod_1   | fc::exists( my->genesis_file ): unable to find genesis file '/opt/eos/bin/data-dir/genesis.json', check --genesis-json argument
  eosiod_1   |     {"f":"/opt/eos/bin/data-dir/genesis.json"}
  eosiod_1   |     thread-0  chain_plugin.cpp:189 plugin_startup
  walletd_1  | 1209368ms thread-0   wallet_api_plugin.cpp:70      plugin_startup       ] starting wallet_api_plugin
  eosiod_1   |
  walletd_1  | 1209368ms thread-0   http_plugin.cpp:242           add_handler          ] add api url: /v1/wallet/create
  eosiod_1   |     {"my->genesis_file.generic_string()":"/opt/eos/bin/data-dir/genesis.json"}
  walletd_1  | 1209368ms thread-0   http_plugin.cpp:242           add_handler          ] add api url: /v1/wallet/get_public_keys
  eosiod_1   |     thread-0  chain_plugin.cpp:210 plugin_startup
```

또 없단다. 결론적으로 Docker 내부의 config.ini가 수정이 안되어서 계속 ```/opt/eos/bin/``` 폴더를 바라보는 것 같다. 
<br> docker-compose.yml 파일을 수정해서 현재 로컬에 있는 config.ini로 컨피규레이션을 바꿔버렸다. <br> 로컬의 ```config.ini```는 경로가 ```/opt/eosio/bin```으로 설정되어 있어서 잘 될듯 싶다.

```diff
diff --git a/Docker/docker-compose.yml b/Docker/docker-compose.yml
index 38116beb..326746e4 100755
--- a/Docker/docker-compose.yml
+++ b/Docker/docker-compose.yml
@@ -13,6 +13,7 @@ services:
       - "8888"
     volumes:
       - eosiod-data-volume:/opt/eosio/bin/data-dir
+      - ./config.ini:/opt/eosio/bin/data-dir/config.ini

   walletd:
     image: eosio/eos
@@ -24,4 +25,4 @@ services:
```

다시 ```docker-compose up``` 커맨드를 실행해보자.

```
$ docker-compose up
Recreating docker_eosiod_1 ... done
Recreating docker_walletd_1 ... done
Attaching to docker_eosiod_1, docker_walletd_1

...

eosiod_1   | 1608044ms thread-0   producer_plugin.cpp:157       plugin_startup       ] Launching block production for 21 producers.
walletd_1  | 1608678ms thread-0   http_plugin.cpp:242           add_handler          ] add api url: /v1/wallet/unlock
eosiod_1   |
eosiod_1   | *******************************
eosiod_1   | *                             *
eosiod_1   | *   ------ NEW CHAIN ------   *
eosiod_1   | *   -  Welcome to EOSIO!  -   *
eosiod_1   | *   -----------------------   *
eosiod_1   | *                             *
eosiod_1   | *******************************
eosiod_1   |
eosiod_1   | Your genesis seems to have an old timestamp
eosiod_1   | Please consider using the --genesis-timestamp option to give your genesis a recent timestamp
eosiod_1   |
eosiod_1   | 1608045ms thread-0   producer_plugin.cpp:167       plugin_startup       ] producer plugin:  plugin_startup() end
eosiod_1   | 1608045ms thread-0   http_plugin.cpp:156           plugin_startup       ] start processing http thread

...

inita generated block 1f2ae552... #4 @ 2018-01-30T08:26:50.000 with 0 trxsuction_loo ]
inita generated block 34e9bfef... #5 @ 2018-01-30T08:26:50.500 with 0 trxsuction_loo ]
initb generated block e55b0ece... #6 @ 2018-01-30T08:26:51.000 with 0 trxsuction_loo ]
initb generated block 2a6e2354... #7 @ 2018-01-30T08:26:51.500 with 0 trxsuction_loo ]
```

이제 docker로 eos의 eosioc와 wallet를 실행했다. 
<br>
드디어 eos의 [tutorial](https://github.com/EOSIO/eos/wiki/Tutorials)를 진행할 수 있다.