# golang-cross-compile.
交叉编译golang 各个平台。

windows 10 home install docker
------------
1\ https://docs.docker.com/docker-for-windows/install/   install it.
------------
2\setting hyPer-V 
------------
//copy following 

pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL


// save it in file as  win10_amd64_install_hyper-v.cmd
------------
3\ run as administrator
------------
4\ install wsl ubuntu
------------
5\ vim ~/.bashrc
------------
    export DOCKER_HOST=tcp://127.0.0.1:2375
    source  ~/.bashrc
   
------------
error during connect: Get http://%2F%2F.%2Fpipe%2Fdocker_engine/v1.40/containers/json: open //./pipe/docker_engine: The system cannot find the file specified. In the default daemon configuration on Windows, the docker client must be run elevated to connect. This error may also indicate that the docker daemon is not running.
-------------
make sure you have configured hyper-v and run win10_amd64_install_hyper-v.cmd
and then run power shell with administrator.

cd "C:\Program Files\Docker\Docker"
./DockerCli.exe -SwitchDaemon

6\ docker pull karalabe/xgo-latest
------------
7\ docker run --rm -v D:/source/proX:/build -v D:/source/src/:/go/src -it karalabe/xgo-latest /bin/bash
------------
8\ docker exec -it "container id" /bin/bash
------------
9\ 
--------
CC=o64-clang CXX=o64-clang++ GOOS=darwin GOARCH=amd64 CGO_ENABLED=1 go build -o bin/game_darwin  -trimpath -ldflags "-s -w" src/proX/cmd/game/main.go
CC=x86_64-w64-mingw32-gcc-posix CXX=x86_64-w64-mingw32-g++-posix GOOS=windows GOARCH=amd64 CGO_ENABLED=1 go build -o bin/game_win -trimpath -ldflags "-s -w" src/proX/cmd/game/main.go
CC=x86_64-linux-musl-gcc GOOS=linux GOARCH=amd64 CGO_ENABLED=1 go build -o bin/game_linux -trimpath -ldflags "-s -w" src/proX/cmd/game/main.go 

// you should download x86_64-linux-musl-cross.tgz and   export PATH=$PATH:$(pwd)/x86_64-linux-musl-cross/bin   //in docker

10\ finaly docker container stop "container id"
------------
