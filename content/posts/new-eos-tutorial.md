+++
title = "EOS에 Smart Contract 생성하기(2018년 9월)"
description = "eos 로컬넷에 Hello world 스마트컨트랙트 작성하기"
tags = [
    "eos",
    "blockchain",
]
date = "2018-09-05T18:15:59-06:00"
menu = "main"
+++

반년동안 아이폰 개발 열심히 했더니 엎어졌다. 스마트 컨트랙트 작성해야되서 다시 공부해봤다. 

## Setup EOS

```shell
$ git clone https://github.com/EOSIO/eos --recursive
$ cd eos
$ git checkout v1.2.3
$ git submodule update --init --recursive
$ ./eosio_build.sh

...

[100%] Built target nodeos
[100%] Linking CXX executable unit_test
[100%] Built target unit_test


	 _______  _______  _______ _________ _______
	(  ____ \(  ___  )(  ____ \\__   __/(  ___  )
	| (    \/| (   ) || (    \/   ) (   | (   ) |
	| (__    | |   | || (_____    | |   | |   | |
	|  __)   | |   | |(_____  )   | |   | |   | |
	| (      | |   | |      ) |   | |   | |   | |
	| (____/\| (___) |/\____) |___) (___| (___) |
	(_______/(_______)\_______)\_______/(_______)

	EOSIO has been successfully built. 00:14:43
...
```

eos 소스코드를 받고 v1.2.3으로 checkout 해준다. 2018년 9월 5일 기준으로 ```v1.2.3``` 버전이 가장 최신 버전이다. 폴더 이동 후 ```eosio_build.sh``` 스크립트를 실행하여 eos를 빌드한다. 빌드하는 데는 약 15분정도 걸린다. 

```shell
$ /usr/local/bin/mongod -f /usr/local/etc/mongod.conf & 
  cd /Users/tak/workspace/buzzler/eos/build; make test
```

위 명령어로 빌드가 잘 됐는지 테스트 가능하다. 참고로 40분 이상 걸린다.

> 
이번에는 한번에 빌드됐지만, 예전에 ```dawn-v4.1``` 버전 빌드할 때 llvm 관련 에러가 났었다. 
```shell
$ brew install llvm@4
$ export LLVM_DIR=/usr/local/Cellar/llvm\@4/4.0.1/lib/cmake
```
- https://github.com/EOSIO/eos/issues/43
- https://github.com/EOSIO/eos/issues/2028
- https://github.com/EOSIO/eos/issues/2998

### Create wallet and Key

빌드가 완료되면 eos 관련 command를 사용할 수 있다. eos 지갑을 만들어보자. ```-n``` 옵션을 주면 지갑의 이름을 설정할 수 있다.

```shell
$ cleos wallet create
"/usr/local/bin/keosd" launched
Creating wallet: default
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"PW5KRyUSVq3Ytht2mwHNx5ZQWYWginSJkiEhU5n6LWL6ehattytK2"

$ cleos wallet create -n tak
Creating wallet: tak
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"PW5KBvksXaoDgkQcCCnkFLbfxMUW5e5TK3hYZi5MhsW5duzzexdnV"
```

지갑 생성 후에, ```~/eosio-wallet``` 폴더와 폴더 안에 지갑 데이터가 생성된다.
```shell
$ cd ~/eosio-wallet
$ ls
config.ini     default.wallet tak.wallet
```

이제 key 쌍을 만들고 지갑에 import 해보자

```shell
# create key

$ cleos create key
Private key: 5JRoLzjFcpBpEUAR3nErMTjRozeonFjBCnfr35t5MC6tVWQdqyW
Public key: EOS8Txt52C9jUD4Pc5LFsceeBy9RKi9MSVEV4WvoaB2KpEjHwyPz8

$ cleos create key
Private key: 5Ja7N54Y9swFfFg4GgUWBBvAGz3jTKhpU5bEHxnFKpQELV5j5vV
Public key: EOS6BFKgddEY1BGQSouFAhh366hyETt6A4x25xdMakwXwsqkUiVop
```

```shell
# import key

$ cleos wallet import 5JRoLzjFcpBpEUAR3nErMTjRozeonFjBCnfr35t5MC6tVWQdqyW
imported private key for: EOS8Txt52C9jUD4Pc5LFsceeBy9RKi9MSVEV4WvoaB2KpEjHwyPz8

$ cleos wallet import 5Ja7N54Y9swFfFg4GgUWBBvAGz3jTKhpU5bEHxnFKpQELV5j5vV
imported private key for: EOS6BFKgddEY1BGQSouFAhh366hyETt6A4x25xdMakwXwsqkUiVop
```

지갑 및 key를 확인하자. 참고로 지갑 옆에 * 표시가 있으면 unlock 되어 있는 상태다.
```shell
$ cleos wallet list
Wallets:
[
  "default *",
  "tak *"
]
```

```shell
$ cleos wallet keys
[[
    "EOS6BFKgddEY1BGQSouFAhh366hyETt6A4x25xdMakwXwsqkUiVop",
    "5Ja7N54Y9swFfFg4GgUWBBvAGz3jTKhpU5bEHxnFKpQELV5j5vV"
  ],[
    "EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
    "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"
  ],[
    "EOS8Txt52C9jUD4Pc5LFsceeBy9RKi9MSVEV4WvoaB2KpEjHwyPz8",
    "5JRoLzjFcpBpEUAR3nErMTjRozeonFjBCnfr35t5MC6tVWQdqyW"
  ]
]
```

### Start wallet demon

wallet 데몬의 기본 포트 값은 8888이다. 현재 회사에서 개발하는 포트랑 겹쳐서 변경해야 한다. 우선 ```eosio-wallet``` 폴더에 있는 config.ini 파일을 수정한다.

```diff
- http-server-address = 127.0.0.1:8888
+ http-server-address = 127.0.0.1:9999
```

이제 wallet 데몬을 다시 실행하자. 아래 명령어로 메뉴얼하게 실행해도 되고, ```cleos wallet``` 관련 명령어를 수행하면 자동으로 데몬이 실행된다.
```shell
$ pkill keosd
$ keosd --http-server-address=localhost:9999
```

### Open and unlock wallet

wallet 데몬을 다시 실행하면 wallet 리스트에 아무 것도 없다. 지갑을 오픈해줘야 한다.

```shell
$ cleos wallet list
"/usr/local/bin/keosd" launched
Wallets:
[]
```

```shell
$ cleos wallet open 
Opened: default

$ cleos wallet open -n tak
Opened: tak

$ cleos wallet list
Wallets:
[
  "default",
  "tak"
]
```

지갑을 확인해보면 * 표시가 없다. 마찬가지로 unlock을 해야한다.

```shell
$ cleos wallet unlock --password PW5KRyUSVq3Ytht2mwHNx5ZQWYWginSJkiEhU5n6LWL6ehattytK2
Unlocked: default

$ cleos wallet unlock -n tak --password PW5KBvksXaoDgkQcCCnkFLbfxMUW5e5TK3hYZi5MhsW5duzzexdnV
Unlocked: tak
```

### Create Account of eos block

eos 계정을 만들기 전에 ```nodeos```를 실행하고 있어야 한다. 아래 명령어로 실행할 수 있다.
<br>
delete-all-block은 기존에 만들었던 block들을 지우고 실행하는 옵션이고, contracts-console 옵션을 줘야 print 메세지가 출력된다.

```shell
$ nodeos -e -p eosio --plugin eosio::chain_api_plugin --plugin eosio::history_api_plugin --delete-all-block --contracts-console
```

이제 계정을 만들어보자. 계정을 만들 때, 아까 생성했던 key 값이 필요하다.

> 커맨드 정보
```shell
$ cleos create account eosio NEW_ACCOUNT OWNER_KEY ACTIVE_KEY
```

OWNER_KEY와 ACTIVE_KEY는 동일하게 했다.
```shell
$ cleos create account eosio tak EOS8Txt52C9jUD4Pc5LFsceeBy9RKi9MSVEV4WvoaB2KpEjHwyPz8 EOS8Txt52C9jUD4Pc5LFsceeBy9RKi9MSVEV4WvoaB2KpEjHwyPz8
$ cleos create account eosio tester EOS6BFKgddEY1BGQSouFAhh366hyETt6A4x25xdMakwXwsqkUiVop EOS6BFKgddEY1BGQSouFAhh366hyETt6A4x25xdMakwXwsqkUiVop
```

계정이 생성되면 key 값을 가지고 account 정보를 확인할 수 있다.

```shell
$ cleos get accounts EOS8Txt52C9jUD4Pc5LFsceeBy9RKi9MSVEV4WvoaB2KpEjHwyPz8
{
  "account_names": [
    "tak"
  ]
}

$ cleos get accounts EOS6BFKgddEY1BGQSouFAhh366hyETt6A4x25xdMakwXwsqkUiVop
{
  "account_names": [
    "tester"
  ]
}
```

기본적인 eos 셋업이 끝났다. 이제 smart contract를 작성할 수 있다.

---

## 헬로월드 Smart Contract

hello world를 프린트하는 smart contract를 작성해보자. 우선 ```hello``` 폴더를 생성한다.
```shell
$ mkdir hello
$ cd hello
$ touch hello.hpp hello.cpp build.sh
```

폴더 안에 ```hello.hpp```, ```hello.cpp``` 파일을 만든다. 각 코드 내용은 다음과 같다. 

```c++
// hello.hpp
#include <eosiolib/eosio.hpp>
#include <eosiolib/print.hpp>

using namespace eosio;

class hello: public eosio::contract {
    public:
        using contract::contract;

        // @abi action
        void hi(account_name user);
};

EOSIO_ABI(hello, (hi))
```

```c++
// hello.cpp
#include<hello.hpp>

void hello::hi(account_name user) {
    print("Hello, ", name{user});
}
```

간단히 설명하면 ```hello```라는 contract를 만들고, 이 contract 안에 ```hi``` 라는 액션을 정의하는 것이다. 자세한 내용은 [eos developer portal](https://developers.eos.io/)을 참고하자.

체인 상에 올리기 위해 이 파일들을 웹어셈블리로 빌드해야 한다. 동일한 경로에 ```build.sh``` 파일을 만들자. 이 스크립트는 cpp파일을 빌드하고, 인자로 들어온 account에 contract를 설정한다. 첫 번째 인자로 account 이름, 두 번째 인자로 contract 이름을 받는다.

```sh
#!/bin/bash

if [[ $# -ne 2 ]]; then
    echo "USAGE: build.sh <account_name> <contract_name> from within the directory"
    exit 1
fi

ACCOUNT=$1
CONTRACT=$2

eosiocpp -o ${CONTRACT}.wast ${CONTRACT}.cpp &&
eosiocpp -g ${CONTRACT}.abi ${CONTRACT}.hpp &&
cleos set contract ${ACCOUNT} ../${CONTRACT}
```

여기까지 됐으면 hello contract를 빌드해보자.

```
$ ./build.sh tak hello
In file included from hello.cpp:1:
In file included from ./hello.hpp:1:
In file included from /usr/local/include/eosiolib/eosio.hpp:7:
In file included from /usr/local/include/eosiolib/action.hpp:7:

...

Generated hello.abi ...
Reading WAST/WASM from ../hello/hello.wasm...
Using already assembled WASM...
Publishing contract...
executed transaction: 2dfa24fb3baf8744359878451f08f5627aa89a9aa15d6aedcb278db6c87bb3b0  1808 bytes  364 us
#         eosio <= eosio::setcode               {"account":"tak","vmtype":0,"vmversion":0,"code":"0061736d01000000013b0c60027f7e006000017e60027e7e00...
#         eosio <= eosio::setabi                {"account":"tak","abi":"0e656f73696f3a3a6162692f312e30000102686900010475736572046e616d65010000000000...
warning: transaction executed locally, but may not be confirmed by the network yet
```

스크립트 실행 후 에러가 없으면 hello contract가 tak 계정에 잘 설정되었다는 뜻이다. ```hi``` action을 날려보자. 

```
$ cleos push action tak hi '["world"]' -p tak
```

위 커맨드를 실행하면 아래 그림과 같이 nodeos에서 ```Hello, world``` 메세지가 출력되는 걸 확인할 수 있다. 

![helloworld](/images/new_helloworld.png)

반년전에 해서 그런지, eos가 열심히 하고있어서 그런지 이번에는 덜 헤맸다.

