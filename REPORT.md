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
$ pushd . 
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
$ git config --global hub.protocol https # Просмотр и установка значения hub.protocol 
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
Вывод:
YAKOVLENKO
origin
$ git remote show ${GITHUB_USERNAME} # Узнаем информацию о сервере ${GITHUB_USERNAME}
Вывод:
* внешний репозиторий YAKOVLENKO
  URL для извлечения: https://github.com/YAKOVLENKO/hunter.git
  URL для отправки: https://github.com/YAKOVLENKO/hunter.git
  HEAD ветка: master
  Внешние ветки:
    master              отслеживается
    pr.new.toolchain.id отслеживается
    testing-packages    отслеживается
  Локальная ссылка, настроенная для «git push»:
    master будет отправлена в master (уже актуальна)
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
hunter_download(PACKAGE_NAME print) # Скачиваем пакет 
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
$ git init #Инициализируем
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
Получение файлов
```ShellSession
$ wget https://github.com/hunter-packages/gate/archive/v0.8.1.tar.gz # Получение пакета
$ tar -xzvf v0.8.1.tar.gz gate-0.8.1/cmake/HunterGate.cmake 
$ mkdir cmake # создание папки mkdir
$ mv gate-0.8.1/cmake/HunterGate.cmake cmake # Перемещаем в cmake
$ rm -rf gate*/ # Удаляем
$ rm *.tar.gz # Удаляем
```
Устанавливаем минимальную обязательную версю cmake для проекта, свойства по умолчанию
```ShellSession
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)
EOF
```
Выводим
```
$ wget https://github.com/${GITHUB_USERNAME}/hunter/archive/v0.19.137.1.tar.gz # Получаем архив
$ export HUNTER_SHA1=`openssl sha1 v0.19.137.1.tar.gz | cut -d'=' -f2 | cut -c2-41` # Определяем HUNTER_SHA1
$ echo $HUNTER_SHA1 # Вывод
Вывод: 3ebd749c5c1d8319feb8e3fb1c86aa6a53f99239
$ rm -rf v0.19.137.1.tar.gz # Удаляем
```
Включаем HunterGate в CMakeLists.txt 
```ShellSession
$ cat >> CMakeLists.txt <<EOF 

include(cmake/HunterGate.cmake)

HunterGate(
    URL "https://github.com/${GITHUB_USERNAME}/hunter/archive/v0.19.137.1.tar.gz"
    SHA1 "${HUNTER_SHA1}"
)
EOF
```
Установка target-объектов
```ShellSession
$ cat >> CMakeLists.txt <<EOF 

project(demo)

hunter_add_package(print) # Добвляем пакет
find_package(print) # Находим пакет

add_executable(demo \${CMAKE_CURRENT_SOURCE_DIR}/sources/demo.cpp) # Добавляем файл для построения
target_link_libraries(demo print) # Привязываем библиотеки

install(TARGETS demo RUNTIME DESTINATION bin) # Устанавливаем
EOF
```
Создаем .gitignore
```ShellSession
$ cat > .gitignore <<EOF 
*build*/
*install*/
*.swp
EOF
```
Помещаем значок MARKDOWN
```ShellSession
$ cat > README.md <<EOF 
[![Build Status](https://travis-ci.org/${GITHUB_USERNAME}/lab10.svg?branch=master)](https://travis-ci.org/${GITHUB_USERNAME}/lab10)
the demo application redirects data from stdin to a file **log.txt** using a package **print**.
EOF
```
Создаем файл для связи с travis
```ShellSession
$ cat > .travis.yml <<EOF 
language: cpp

script:   
- cmake -H. -B_build
- cmake --build _build
EOF
```
Проверка конфигурации
```ShellSession
$ travis lint 
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
Построение, создание папки artifacts, вывод текста
```ShellSession
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install 
Вывод см. ниже
$ cmake --build _build --target install 
$ mkdir artifacts && cd artifacts 
$ echo "text1 text2 text3" | ../_install/bin/demo 
$ cat log.txt 

Вывод: 
-- [hunter] Initializing Hunter workspace (3ebd749c5c1d8319feb8e3fb1c86aa6a53f99239)
-- [hunter]   https://github.com/YAKOVLENKO/hunter/archive/v0.19.137.1.tar.gz
-- [hunter]   -> /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/Download/Hunter/0.19.137/3ebd749
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter
-- [hunter] [ Hunter-ID: 3ebd749 | Toolchain-ID: e1266bb | Config-ID: 83da1ac ]
-- [hunter] PRINT_ROOT: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Install (ver.: 0.1.0.0)
-- [hunter] Building print
loading initial cache file /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/cache.cmake
loading initial cache file /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/args.cmake
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Build
Scanning dependencies of target print-Release
[  6%] Creating directories for 'print-Release'
[ 12%] Performing download step (download, verify and extract) for 'print-Release'
-- downloading...
     src='https://github.com/YAKOVLENKO/lab09/archive/v0.1.0.0.tar.gz'
     dst='/home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/Download/print/0.1.0.0/dd2c3fd/v0.1.0.0.tar.gz'
     timeout='none'
-- [download 0% complete]
-- [download 1% complete]
-- [download 2% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 83% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- downloading... done
-- verifying file...
     file='/home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/Download/print/0.1.0.0/dd2c3fd/v0.1.0.0.tar.gz'
-- verifying file... done
-- extracting...
     src='/home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/Download/print/0.1.0.0/dd2c3fd/v0.1.0.0.tar.gz'
     dst='/home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Source'
-- extracting... [tar xfz]
-- extracting... [analysis]
-- extracting... [rename]
-- extracting... [clean up]
-- extracting... done
[ 18%] No patch step for 'print-Release'
[ 25%] No update step for 'print-Release'
[ 31%] Performing configure step for 'print-Release'
loading initial cache file /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/cache.cmake
loading initial cache file /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/args.cmake
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Build/print-Release-prefix/src/print-Release-build
[ 37%] Performing build step for 'print-Release'
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
[ 43%] Performing install step for 'print-Release'
[100%] Built target print
Install the project...
-- Install configuration: "Release"
-- Installing: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/lib/libprint.a
-- Installing: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/include
-- Installing: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/include/print.hpp
-- Installing: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/cmake/print-config.cmake
-- Installing: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/cmake/print-config-release.cmake
loading initial cache file /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/args.cmake
[ 50%] Completed 'print-Release'
[ 50%] Built target print-Release
Scanning dependencies of target print-Debug
[ 56%] Creating directories for 'print-Debug'
[ 62%] Performing download step (download, verify and extract) for 'print-Debug'
-- verifying file...
     file='/home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/Download/print/0.1.0.0/dd2c3fd/v0.1.0.0.tar.gz'
-- verifying file... done
-- extracting...
     src='/home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/Download/print/0.1.0.0/dd2c3fd/v0.1.0.0.tar.gz'
     dst='/home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Source'
-- extracting... [tar xfz]
-- extracting... [analysis]
-- extracting... [rename]
-- extracting... [clean up]
-- extracting... done
[ 68%] No patch step for 'print-Debug'
[ 75%] No update step for 'print-Debug'
[ 81%] Performing configure step for 'print-Debug'
loading initial cache file /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/cache.cmake
loading initial cache file /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/args.cmake
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Build/print-Debug-prefix/src/print-Debug-build
[ 87%] Performing build step for 'print-Debug'
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprintd.a
[100%] Built target print
[ 93%] Performing install step for 'print-Debug'
[100%] Built target print
Install the project...
-- Install configuration: "Debug"
-- Installing: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/lib/libprintd.a
-- Up-to-date: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/include
-- Up-to-date: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/include/print.hpp
-- Installing: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/cmake/print-config.cmake
-- Installing: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/Install/cmake/print-config-debug.cmake
loading initial cache file /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print/args.cmake
[100%] Completed 'print-Debug'
[100%] Built target print-Debug
-- [hunter] Build step successful (dir: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/3ebd749/e1266bb/83da1ac/Build/print)
-- [hunter] Cache saved: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/hunter/hunter/_Base/Cache/raw/7b95b97eb4f86e4d7c8c7c230375bb3987687a08.tar.bz2
-- Configuring done
-- Generating done
-- Build files have been written to: /home/user1/YAKOVLENKO/workspace/projects/hunter/projects/lab10/_build


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
