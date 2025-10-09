## Docker 그리고 Container

#### Cloud Native Application
- cloud native application의 기본 요건 중 하나는 application을 컨테이너로 만드는 것이고, 요즘 application은 aws, azure등의 cloud를 이용해 배포. docker를 활용하면 일관된 환경을 구축할 수 있어 개발을 할때 용이하다.

#### Hello Docker
- docker는 하나의 프로그램이 아닌 여러개의 프로그램으로 이루어짐. docker desktop은 필요한 프로그램을 한번에 설치할 수 있도록 도와주고 gui도 제공한다. 실무환경에서는 GUI 보다는 CLI를 많이 사용한다. CLI에 익숙해지면 GUI보다 더 빨리 많이 쉽게 처리가 가능하다. 

#### 컨테이너와 이미지
- 컨테이너는 application을 실행하기 위한 격리된 경량 프로세스이다. 프로세스란 실행중인 프로그램을 의미한다. 예를 들어, A와B라는 두개의 프로그램을 동시에 실행한다고 하면 각각이 하나의 프로세스가 된다. 프로세스는 메모리, 파일시스템, 네트워크 등의 자원을 사용하고, 이때 프로세스는 서로가 서로에게 간섭하지 못하도록 메모리 수준으로 격리되지만 컨테이너는 파일시스템과 네트워크까지 서로 간섭하지 못하도록 격리시킨다. 격리가 필요한 이유는, 보통 애플리케이션을 실행할때 애플리케이션은 각각이 하나의 컴퓨터에서 실행되지 않는다. 하나의 컴퓨터에서 실행하는 애플리케이션도 격리된 환경이 필요하다. 그래야 서로의 간섭을 줄이고 특정 애플리케이션의 장애가 다른 애플리케이션에게 영향을 주지 않기 때문이다. 컴퓨터의 자원은 요구수준에 따라 격리수준을 달리한다. 호스트, 즉 컴퓨터 자원의 주인 (컴퓨터 그 자체)의 자원중 어떤 계층에서 격리하느냐에 따라 격리수준이 달라진다. 더 낮은 계층에서 격리할 수록 온전한 컴퓨터로 취급이 된다. 
```
메모리
--------
파일시스템 / 네트워크 / 라이브러리
--------
운영체제
```
#### 격리수준
- 운영체제 수준에서 격리하는 기술을 하이퍼바이저라고 한다. 하이퍼바이저 단위로 격리된 것(단위)을 vm(가상머신)이라고 부른다. 가상머신은 높은 수준의 격리를 제공해서 보안상 이점이 있으나, 실행 단위가 무거워지고 초기실행이 무겁다. 다음으로, 운영체제는 격리에서 제외하고 파일시스템, 네트워크, 라이브러리, 메모리 등을 격리해서 제공하는 것을 컨테이너라고 한다. 컨테이너 기술로 격리된 자원의 단위를 컨테이너라 하고, 운영체제를 포함하지 않아 vm보다 훨씬 가볍다. 현대 어플리케이션은 다양한 기능을 제공하면서 트래픽에 대응하면서 빠르게 구동해야 하므로 컨테이너가 유용하다. 그런데 컨테이너를 다른 호스트에서 동일하게 실행해야 한다. 이때, 이미지를 통해 다른 호스트들에도 동일한 컨테이너를 실행할 수 있도록 제공할 수 있다. 이미지는 컨테이너를 생성하기 위한 모든 것을 포함한 파일 묶음이다. 소스코드나 어떤 명령어나 실행방법에 대한 모든 것을 포함한다. 이렇듯 이미지는 파일 묶음이기 때문에 쉽게 전달할 수 있다. 이미지는 버전관리 체계를 가진다. 버전별로 태그를 설정할 수 있다. 이미지의 크기가 너무 커지면 네트워크를 통해 주고 받을때 효율이 떨어져서 레이어라는 방식을 통해 나누어서 저장할 수 있어, 중복 저장을 방지 할 수 있다.

#### 이미지 관리
- 이미지는 컨테이너를 실행하기 위한 모든 정보를 포함한다. 그 중에 가장 기본적으로 애플리케이션 실행환경이 필요하다. nextjs라면 node환경이 django라면 python환경이 필요한 등 말이다. 이미지는 대부분 dockerhub를 통해서 사용할 수 있다.
docker pull image [application] 뒤의 @를 통해 다이제스트를 붙이면 특정 이미지를 받아올 수 있고, 또는 뒤에 특정 버전을 붙이면 특정 버전을 받아올 수 있다. docker image prune을 통해 태그가 없는 사용하지 않는 이미지들을 삭제할 수 있다. 그러나 이런 태그가 없는 이미지 뿐만 아니라 모든 이미지를 삭제하고 싶다면 docker image prune -a를 통해 모든 이미지를 삭제하고 싶다.

#### dockerfile 작성
- 이미지는 여러개의 레이어로 구성되어있다. 그래서 이미지를 만드는 것은 여러개의 레이어를 쌓아올리는 과정과 같다. dockerfile은 레이어를 어떻게 쌓아올릴것인지에 대해 계획을 작성하는 파일이다. 정해진 문법에 따라 dockerfile을 작성하면 docker엔진이 순서대로 쌓아 파일을 만든다. 

###### node를 설치한다. (애플리케이션을 실행하는데 필요한 환경을 준비, 가장 밑에 깔리는 layer 준비), ARG 커맨드를 통해 버전을 정의해주면 도커파일 내에서 일일이 수정해줄 필요 없다.

###### 소스코드를 다운로드한다.

###### 소스코드의 최상위 디렉토리로 이동한다.

###### 소스코드를 실행할 때 필요한 파일을 다운로드한다. (npm ci)

###### 소스코드를 빌드한다. (npm run build)

###### 환경변수를 정의한다. (PORT)

###### 서버를 실행한다. (npm run start)

#### node.js를 기준으로 작성된 dockerfile
```
# node를 설치한다. (애플리케이션을 실행하는데 필요한 환경을 준비, 가장 밑에 깔리는 layer 준비)
#FROM 베이스 이미지
ARG NODE_VERSION
FROM node:${NODE_VERSION}


# 소스코드를 다운로드한다.
# COPY [복사할 경로] [붙여넣기할 경로]
COPY . /app 
# 현재 디렉토리의 내용을 app 디렉토리로 복사한다.
# 상대경로의 기준이 build context(도커파일이 있는 위치)이다.


# 소스코드의 최상위 디렉토리로 이동한다.
WORKDIR /app

# 소스코드를 실행할 때 필요한 파일을 다운로드한다. (npm ci)
RUN npm ci

# 소스코드를 빌드한다. (npm run build)
RUN npm run build

# RUN npm ci && npm run build 라는 하나의 명령어로 작성할 경우
# 레이어가 줄어들어 훨씬 좋다.

# 환경변수를 정의한다. (PORT)
ENV PORT==3000

# 서버를 실행한다. (npm run start)
ENTRYPOINT ["npm", "run", "start"]
# ENTRYPOINT를 정의할때는 조금 특이한 형태로 정의한다.
```
#### 이미지 빌드/배포
- dockerfile은 설계도면이고 build context는 설계할 위치이다. docker buildx build라는 명령어를 통해 option과 build context를 파라미터로 받는다. '-t'옵션을 통해 빌드할 이미지의 레포지토리와 태그를 지정하는 위치이다. 한번에 여러개의 태그를 지정하고 싶으면 태그를 여러개 지정하면 된다. 태그를 지정하지 않으면 태그가 없는 이미지가 된다.build arg 옵션으로 arg 명령어를 통해 node_version이라는 변수에 버전을 지정해 줄 수 있다. f옵션은 사용할 도커파일의 경로를 설정해주는 옵션이다. pull 옵션은 베이스 이미지가 로컬에 저장되어 있어도 빌드할때마다 다시 강제로 다운로드 하기위한 옵션이다. 

#### Docker 이미지 배포
-   docker buildx build \
    > -t [사용자명]/mbti[repo명]:embedded-db[태그] \
    > -t devwoo41/mbti[repo명] [태그가 없으므로 latest] \  '' 두개의 태그 (embedded-db, latest) 모두에 등록한다는 의미이다.           
    > --build-arg NODE_VERSION=20.15.1 \
    > -f./Dockerfile [사용할 도커파일의 경로 지정] \
    > --pull \
    > .
- docker image push [사용자이름]/[repo]:[tag], 와 같이 배포를 하면 성공적으로 push가 되고 docker hub에서 확인해 볼 수 있다.

#### 컨테이너 실행&관리
- 컨테이너를 실행하거나 관리하는 명령어는 docker container로 시작한다. 'docker container run [옵션] [이미지]' 라는 명령어를 통해 컨테이너를 실행한다. 
``` 
docker container run \
--name mbti \  
-e PORT=3000 \
--env-file .docker.env \
--rm \
-d \
[사용자 이름]/[repo명]:[태그]
```
- name 옵션을 통해 컨테이너 명을 지정해 줄 수 있다. 다음으로 e 옵션은 환경변수를 정의하는 옵션이다. env file 옵션으로 환경변수를 .docker.env 라는 파일로 정리해서 설정할 수 있다. rm옵션은 컨테이너를 종료하고 자동으로 삭제까지 해 줄수 있는 옵션이다. 도커에서는 일반적으로 종료한다고 바로 삭제되지 않고 로그를 확인할 수 있게 컨테이너 정보가 남아있어 rm 옵션을 통해 자동으로 삭제할 수 있게 해 주는 옵션이다. d 옵션은 컨테이너를 백그라운드로 실행하게 해준다. 
- 현재 실행중인 컨테이너는 docker container ls를 통해 확인할 수 있다. 만약 컨테이너 내부를 확인하고 싶으면 'docker container exec [옵션] [컨테이너] ls /app' 을 통해서 확인 할 수 있다.
- 컨테이너 내부를 shell을 통해서 접속할 수 있다. 'docker container exec -it [컨테이너 이름] bash'를 통해 쉘창으로 컨테이너 내부를 터미널을 통해 접속 할 수 있다. 
- 'docker container logs -f mbti'라는 명령어를 통해 로그를 확인할 수 있다. docker 내부에서 curl 명령어를 통해 로그를 확인 할 수 있다.
- docker container exec mbti \
- > curl localhost:3000/api/healthcheck

- 이 명령어를 실행한 경우 log창에 요청이 떠야 한다.

- docker container stop [컨테이너 이름]
- docker container ls -a를 통해 모든 컨테이너를 확인해 종료되었는지 확인할 수 있다.
- docker container prune 명령어를 통해 현재 실행중이지 않은 모든 컨테이너를 삭제할 수 있다.

#### 네트워크 1
- 컨테이너는 격리된 격량 프로세스이다. 컨테이너 입장에서는 browser에서 접속하는 것도 외부이기에 격리되어 통신이 불가능하다. docker network를 통해 접속/통신할 수 있다. docker network에서는 통신할 수 있는 방법이 두가지가 있는데, 하나는 컨테이너의 네트워크를 통해 통신하는 것이고, 두번째는 호스트의 네트워크를 통해 통신하는 방식이다. docker network도 명령어를 통해 관리한다.
- docker network라는 명령어로 시작한다.
- 'docker network ls' 라는 명령어를 통해 네트워크 종류를 확인할 수 있다. docker network에는 기본으로 제공하는 bridge, host, null의 network가 있는데, 이런 network의 종류를 드라이버라고 한다. bridge 드라이버가 일반적으로 가장 많이 사용된다.
- 'docker network create [네트워크명]' 이라는 명령어를 통해 bridge 드라이버를 이용한 네트워크를 생성할 수 있다. 이전 container의 명령어와 같이 rm, prune 명령어를 통해 네트워크를 삭제할 수 있다. 'docker network rm [네트워크명]' 을 통해 네트워크를 삭제할 수 있다.
- 'mynetwork'라는 네트워크를 만든 경우, 컨테이너를 run할 때, 만든 네트워크로 연결하고 싶을때에는 앞선 docker container run 명령어에 --network를 통해 네트워크 옵션을 추가해주면 된다.
``` 
docker container run \
--name mbti \  
-e PORT=3000 \
--env-file .docker.env \
--rm \
-d \
--network mynetwork \
[사용자 이름]/[repo명]:[태그]
```
- 컨테이너에 연결된 네트워크는 'docker container inspect [컨테이너명] '라는 명령어를 통해 확인할 수 있다.

#### 네트워크 2
- 이번에는 호스트의 네트워크를 연결해서 통신하는 방법이다. 호스트의 네트워크에 연결한다는건 네트워크의 특정포트를 외부에 공개하고 컨테이너 포트의 특정 포트와 짝지어 두는 포트 매핑(포트 바인딩)이다. 컨테이너를 실행 시키면서 'p(publish)' 옵션을 추가함으로써 바인딩이 가능하다. 
- docker container run \ ... *-p 4000:3000* '-p 4000:3000'의 의미는 호스트의 4000번 포트를 컨테이너의 3000번 포트와 매핑해 준다는 의미이다. 그러면 호스트의 4000번 포트 (localhost:4000) 을 통해 확인할 수 있다. 흔히 EXPOSE와 publish를 혼동하는 경우가 많은데, 두 옵션의 가장 큰 차이는 실제 포트 개방의 여부이다. publish 옵션은 실제 포트를 개방하는 반면, EXPOSE는 실제 포트를 개방하지 않는다. EXPOSE를 쓰는 이유는 어떤 포트를 개방한다 라는 정보를 표시하기 위함이다.

#### 볼륨
- 컨테이너의 파일시스템은 네트워크와 같이 격리되어 구성된다. 그래서 컨테이너가 실행되는동안 생성되는 다양한 파일은 외부에서 접근할 수 없고, 컨테이너가 삭제될때 모두 함께 삭제된다. 만약 컨테이너 외부에서 파일에 접근하고자 하거나 컨테이너를 삭제해도 파일을 유지하고자 한다면 docker의 volume기능을 고려해야한다. volume이란 컨테이너 내부의 디렉토리와 연결된 컨테이너 외부의 디렉토리이다. 컨테이너 내부에 mount된 디렉토리에 파일을 생성한다면 사실은 volume에 생성된다. 
- 볼륨 옵션은 '-v'라는 형태로 줄 수 있는데, 이 또한 컨테이너를 run할때 옵션을 줌으로써 볼륨을 생성(?),연결할 수 있다. 
- '-v [호스트 쪽 경로 또는 볼륨 이름]:[컨테이너 내부 경로]' 와 같이 표현함으로써 docker가 관리하는 공간에 볼륨이 자동생성되고, 이 볼륨은 컨테이너 내부 경로와 마운트된다.
- docker container inspect [컨테이너] 를 통해서 mounts 옵션에 가보면 내가 어떤 경로에 mount하고 있는지 확인할 수 있다.
- 내 작업 디렉토리 내부에 바인드 디렉토리를 만들면 즉각적으로 확인할 수 있다.

#### Docker Compose
- 이미지 빌드, 컨테이너 실행, 볼륨과 네트워크 생성 등 실행,작성해야 할 것들이 너무 많다. 이러한 문제를 해결하기 위해 Docker Compose가 있다. Docker Compose는 여러 컨테이너를 단순히 관리할 수 있는데, 이미지 빌드, 컨테이너 실행, 볼륨과 네트워크 생성등을 단 하나의 파일로 관리할 수 있다. 그리고 이 compose 파일은 계층 관리에 적합한 yaml 파일로 생성된다. 
- 'docker-compose.yaml' 내부에 name 속성으로 프로젝트 이름을 지정해 줄 수 있다. 다음으로 서비스 속성(애플리케이션)은 컨테이너에 대한 정보를 정의한다. 따라서 네임과 달리 반드시 정의해주어야 한다. 예를들어 app과 db라는 두개의 컨테이너가 있는 경우, services: 하단에 app: 과 db: 이런식으로 추가해 준다. yaml에서는 들여쓰기로 하위속성을 정의해준다. 
- 결론적으로, docker compose에서 네트워크, 포트, 볼륨, env, 앱 등 많은 것들을 설정해주고 docker compose up 등을 통해서 docker-compose.yaml에서 읽어온 후, 명세에 맞게 container와 네트워크등 환경을 실행해준다. 그리고 docker compose down -v를 통해서 실행중인 컨테이너, 네트워크, 볼륨 등등 compose로 생성되었던 것들을 모두 삭제해준다. 
#### 예시 docker-compose.yaml
```
name: mbti
services:
  app:
    image: devwoo41/mbti:mysql
    build:
      args:
        - NODE_VERSION=20.15.1
      dockerfile: ./Dockerfile
      pull: true
      context: .
    container_name: app
    environment:
      - PORT=3000
      - DB_HOST=db
      - DB_PORT=3306
      - DB_NAME=db_mbti
      - DB_USERNAME=user_mbti
      - DB_PASSWORD=pw_mbti
    networks:
      - mbti-net
    ports:
      - 4000:3000
  db:
    image: mysql:8.3.0
    container_name: db
    environment:
      - MYSQL_ROOT_PASSWORD=root1234
      - MYSQL_DATABASE=db_mbti
      - MYSQL_USER=user_mbti
      - MYSQL_PASSWORD=pw_mbti
    networks:
      - mbti-net
    volumes:
      - mbti-vol:/var/lib/mysql
networks:
  mbti-net:
    name: mbti-net
volumes:
  mbti-vol:
    name: mbti-vol
```

#### docker compose의 유용한 기능으로 서비스 간 의존관계 설정하기
- 앞서 작성한 docker compose의 app 서비스는 db 서비스가 없으면 동작할 수 없고 이런 관계를 app 서비스가 db 서비스에 의존한다고 하고, 이런 서비스의 경우에는 구동순서를 제어해야 한다. 시작할때는 db 서비스가 먼저 시작하고 app 서비스가 후에 시작하고, 종료될 때는 app 서비스가 먼저 종료되고 db 서비스가 종료되는 식으로 말이다. docker compose 에서는 의존관계를 정리해주는 depends_on을 통해서 의존관계를 정리할 수 있다.
- 만약 compose 파일에 depneds_on을 지정해 주지 않을 경우, 서비스의 실행 순서는 무작위이다. 
- 그러나 app 서비스의 하위에 depends_on: \  -db 라고 정의를 해주고 compose up을 실행하는 경우, 항상 db가 먼저 실행되고 app이 실행 된다.
- 유의할 점은, depends_on은 컨테이너 상태를 기준으로 실행된다. 이 때문에, 컨테이너 실행에서 문제가 될 수 있다. 예를 들어서, db 컨테이너가 실행이 되고, mysql이 정상적으로 실행이 되지 않을 경우, app 컨테이너는 db 컨테이너 실행을 기준으로 실행이 되기 때문에, 정상적으로 실행이 된다. 만약, db 실행이 정상적으로 실행이 모두 되고서, app 컨테이너도 그에 따라 실행이 되게 하려면 *condition* 과 *healthcheck* 속성 등을 추가하면 된다. 즉, depends_on 하나만으로는 의존성을 완벽히 관리하기는 어렵다는 것이다. 

#### RUN, CMD, ENTRYPOINT의 차이
- RUN 명령어의 경우, 이미지 빌드시에 실행이 된다. CMD와 ENTRYPOINT의 경우, 컨테이너 실행시에 실행이 되는데, ENTRYPOINT가 CMD 보다 조금 더 강하게 작용된다. 한마디로 CMD의 경우 CMD["npm","run","start"]가 작성되어 있지만, 컨테이너 실행시에 docker container run [컨테이너] bash를 할 경우, npm run start는 무시되고 bash가 실행이 되지만, ENTRYPOINT로 실행한 것은 고정된 명령으로 무시되지 않는다. 이러한 연유로 ENTRYPOINT로는 항상 실행될 기본 명령어 ex. ["npm","run"] 등이 작성되고, CMD로는 ["start"] 등을 집어넣는 등, CMD는 ENTRYPOINT의 파라미터로서 사용된다. CMD가 조금 더 유연하기 때문이다.

#### ARG와 ENV
- ARG의 경우, 이미지를 빌드할 때에 버전이나 변수들을 지정해주고 없어진다. ENV의 경우에는, 컨테이너 실행을 할 때에 환경변수와 같이 동작을 하는데, 예로써 쉽게말해, 만약 빌드를 할 당시에 ARG PORT=3000으로 지정해주고 컨테이너를 실행한뒤에 echo $PORT를 할경우, 아무것도 출력되지 않는다. 따라서 실무에서는 ARG로 빌드시 값을 받고 -> ENV로 넘겨주는 패턴을 자주 쓴다. 
```
ARG NODE_VERSION=20.15.1
FROM node:${NODE_VERSION}

ARG APP_PORT=3000
ENV PORT=${APP_PORT}

RUN echo "Build-time PORT: ${APP_PORT}"
CMD ["npm", "run", "start"]
```

#### 최적화
```
Docker 이미지를 잘 최적화하려면
① 레이어 캐시를 활용해 빌드 시간을 줄이고,
② 멀티스테이지 빌드로 이미지 크기를 줄이고,
③ 플랫폼 옵션과 메타데이터로 가용성과 재사용성을 높이면 된다. 
```