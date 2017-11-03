## Laboratory work X

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```ShellSession
$ open https://github.com/ruslo/hunter
```

## Tasks

- [X] 1. Создать публичный репозиторий с названием **lab10** на сервисе **GitHub**
- [X] 2. Сгенирировать токен для доступа к сервису **GitHub** с правами **repo**
- [X] 3. Выполнить инструкцию учебного материала
- [X] 4. Ознакомиться со ссылками учебного материала
- [X] 5. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Задаем значения переменным
```ShellSession
$ export GITHUB_USERNAME=<имя_пользователя> # Задаем значение GITHUB_USERNAME
$ export GITHUB_TOKEN=<сгенирированный_токен> # Задаем значение GITHUB_TOKEN
```
Скачиваем и устанавливаем дополнение hub
```ShellSession
$ cd ${GITHUB_USERNAME}/workspace # Переходим в папку workspace
$ pushd . # 
$ source scripts/activate # Указываем источник
$ go get github.com/github/hub # Скачиваем и устанавливаем
```
Задаем значения в hub
```ShellSession
$ mkdir ~/.config # Создаем папку .config
$ cat > ~/.config/hub <<EOF # Создаем файл hub и прописываем в него значения 
github.com:
- user: ${GITHUB_USERNAME}
  oauth_token: ${GITHUB_TOKEN}
  protocol: https
EOF
$ git config --global hub.protocol https #
```
Получение версии архивом
```ShellSession
$ wget https://github.com/${GITHUB_USERNAME}/lab09/archive/v0.1.0.0.tar.gz # получаем архив
$ export PRINT_SHA1=`openssl sha1 v0.1.0.0.tar.gz | cut -d'=' -f2 | cut -c2-41` # Задаем значения PRINT_SHA1 - чексуммы
$ echo $PRINT_SHA1 # Выводим на экран
$ rm -rf v0.1.0.0.tar.gz # Удаляем 
```
Клонируем и делаем fork
```ShellSession
$ git clone https://github.com/ruslo/hunter projects/hunter #Клонируем hunter в projects
$ cd projects/hunter && git checkout v0.19.137 # Переходим в hunter на v0.19.137
$ git remote show # Показываем удаленные серверы
$ hub fork # Делаем fork
$ git remote show # Показываем удаленные серверы
$ git remote show ${GITHUB_USERNAME} # Узнаем информацию о сервере ${GITHUB_USERNAME}
```
Указываем нужные значения в hunter.cmake
```ShellSession
$ mkdir cmake/projects/print # Создаем новую папку
$ cat > cmake/projects/print/hunter.cmake <<EOF # Включаем библиотеки hunter, настраиваем  
include(hunter_add_version)
include(hunter_cacheable)
include(hunter_cmake_args)
include(hunter_download)
include(hunter_pick_scheme)

hunter_add_version( # Добавляем версию
    PACKAGE_NAME
    print
    VERSION
    "0.1.0.0"
    URL
    "https://github.com/${GITHUB_USERNAME}/lab09/archive/v0.1.0.0.tar.gz"
    SHA1
    ${PRINT_SHA1}
)

hunter_pick_scheme(DEFAULT url_sha1_cmake) # Выбор схемы построения текущего проекта

hunter_cmake_args( # Указываем аргументы hunter (без примеров, без тестов)
    print
    CMAKE_ARGS
    BUILD_EXAMPLES=NO
    BUILD_TESTS=NO
)
с(print)
hunter_download(PACKAGE_NAME print) # Скачиваем пакет PACKAGE_NAME
EOF
```
Выбираем версию для построения
```ShellSession
$ cat >> cmake/configs/default.cmake <<EOF
hunter_config(print VERSION 0.1.0.0)
EOF

```
Выкладываем изменения
```ShellSession
$ git add . # Выбираем изменения
$ git commit -m"added print package" # Добавляем комментарии
$ git push ${GIHUB_USERNAME} master # Добавляем на сервер master
$ git tag v0.19.137.1 # Просматриваем версии
$ git push ${GIHUB_USERNAME} master --tags # Добавляем веток на сервер
$ cd .. #
```
Соединение с сервером
```ShellSession
$ export HUNTER_ROOT=`pwd`/hunter # Определение переменной для добавления новго пакета
$ mkdir lab10 && cd lab10 # Создаем папку lab10 И переходим туда
$ git init # Инициализируем
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab10 # Сообщаем локальному репозиторию, где находится главный репозиторий
```
Создание файла с кодом cpp
```ShellSession
$ mkdir sources # Создание папки 
$ cat > sources/demo.cpp <<EOF # Заполенение demo.cpp
#include <print.hpp>

int main(int argc, char** argv) {
	std::string text;
	while(std::cin >> text) {
		std::ofstream out("log.txt", std::ios_base::app);
		print(text, out);
		out << std::endl;
	}
}
EOF
```

```ShellSession
$ wget https://github.com/hunter-packages/gate/archive/v0.8.1.tar.gz # Получение пакета
$ tar -xzvf v0.8.1.tar.gz gate-0.8.1/cmake/HunterGate.cmake #
$ mkdir cmake # создание папки mkdir
$ mv gate-0.8.1/cmake/HunterGate.cmake cmake #
$ rm -rf gate*/ #
$ rm *.tar.gz #
```

```ShellSession
$ cat > CMakeLists.txt <<EOF #
cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)
EOF
```

```
$ wget https://github.com/${GITHUB_USERNAME}/hunter/archive/v0.19.137.1.tar.gz #
$ export HUNTER_SHA1=`openssl sha1 v0.19.137.1.tar.gz | cut -d'=' -f2 | cut -c2-41` #
$ echo $HUNTER_SHA1 #
$ rm -rf v0.19.137.1.tar.gz #
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF #

include(cmake/HunterGate.cmake)

HunterGate(
    URL "https://github.com/${GITHUB_USERNAME}/hunter/archive/v0.19.137.1.tar.gz"
    SHA1 "${HUNTER_SHA1}"
)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF #

project(demo)

hunter_add_package(print)
find_package(print)

add_executable(demo \${CMAKE_CURRENT_SOURCE_DIR}/sources/demo.cpp)
target_link_libraries(demo print)

install(TARGETS demo RUNTIME DESTINATION bin)
EOF
```

```ShellSession
$ cat > .gitignore <<EOF #
*build*/
*install*/
*.swp
EOF
```

```ShellSession
$ cat > README.md <<EOF #
[![Build Status](https://travis-ci.org/${GITHUB_USERNAME}/lab10.svg?branch=master)](https://travis-ci.org/${GITHUB_USERNAME}/lab10)
the demo application redirects data from stdin to a file **log.txt** using a package **print**.
EOF
```
Устанавливаем язык
```ShellSession
$ cat > .travis.yml <<EOF #
language: cpp

script:   
- cmake -H. -B_build
- cmake --build _build
EOF
```

```ShellSession
$ travis lint #
```
Загружаем изменения на сервер
```ShellSession
$ git add . # Выбираем все изменения
$ git commit -m"first commit" # Добавляем комментарий
$ git push origin master # Выкладываем изменения на сайт
```
Связываемся с travis
```ShellSession
$ travis login --auto # Авторизуемся
$ travis enable # Активируем ЛР10
```

```ShellSession
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install #
$ cmake --build _build --target install #
$ mkdir artifacts && cd artifacts #
$ echo "text1 text2 text3" | ../_install/bin/demo #
$ cat log.txt #
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=10
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [hub](https://hub.github.com/)
- [polly](https://github.com/ruslo/polly)
- [conan](https://conan.io)

```
Copyright (c) 2017 Братья Вершинины
```
