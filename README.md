[![Build Status](https://travis-ci.org/justddreamm/lab08.svg?branch=master)](https://travis-ci.org/justddreamm/lab08)

# Лабораторная работа №08 Лагов Сергей ИУ8-22

Указываем операционную систему

```
$ cat > Dockerfile <<EOF
FROM ubuntu:18.04
EOF
```

Говорим *Docker*, что следует обновить систему и установить пакеты

```
$ cat >> Dockerfile <<EOF
RUN apt update
RUN apt install -yy gcc g++ cmake
EOF
```

Говорим текущий каталог

```
$ cat >> Dockerfile <<EOF
COPY . print/
WORKDIR print
EOF
```

Собираем *CMake*

```
$ cat >> Dockerfile <<EOF
RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install
EOF
```

Мы создали скрипт, теперь вызываем *docker* и выводим список ВМ

```
$ docker build -t logger .
$ docker images
```

Запускаем ВМ, *( -it )* терминал и *( -v )* подключаем каталог

```
$ mkdir logs
$ docker run -it -v "$(pwd)/logs/:/home/logs/" logger
text1
text2
text3
<C-D>
```

Далее редактируем *.travis.yml*

С введёнными *Environmental Variables* в *Travic CI*, билд успешно собирается
