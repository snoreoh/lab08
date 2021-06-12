[![Build Status](https://travis-ci.org/justddreamm/lab07.svg?branch=master)](https://travis-ci.org/justddreamm/lab07)

# Лабораторная работа №07 Лагов Сергей ИУ8-22

```
$ mkdir -p cmake
$ wget https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake -O cmake/HunterGate.cmake
$ gsed -i -e '/cmake_minimum_required(VERSION 3.4)/a\     
include("cmake/HunterGate.cmake") \
HunterGate( \
    URL "https://github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz" \
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d" \
) \
' CMakeLists.txt
```

Подключили *hunter*

```
$ git rm -rf third-party/gtest
$ gsed -i -e '/set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")/a\
hunter_add_package(GTest) \
find_package(GTest CONFIG REQUIRED) \
' CMakeLists.txt
$ gsed -i -e 's/add_subdirectory(third-party/gtest)/' CMakeLists.txt
$ gsed -i -e 's/gtest_main/GTest::main/' CMakeLists.txt
```

*gtest* мы должны удалить - *hunter* сам подключит тестики

```
$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build _builds
$ cmake --build _builds --target test
$ ls -la $HOME/.hunter
```

Меняем *BUILD TESTS* на *ON*, запускаем сборки и печатаем информацию о *.hunter*

```
$ rm -rf _builds
$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build _builds
$ cmake --build _builds --target test
```

Cкачали репозиторий *hunter* с *github*, удаляем *_builds* и запускаем сборки

```
$ cat $HUNTER_ROOT/cmake/configs/default.cmake | grep GTest
$ cat $HUNTER_ROOT/cmake/projects/GTest/hunter.cmake
$ mkdir cmake/Hunter
$ cat > cmake/Hunter/config.cmake <<EOF
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF
# add LOCAL in HunterGate function
```

Печатаем в консоль все строки указанного файла, в которых содержится подстрока *GTest*, затем печатаем файл *hunter.cmake*. После создания папки *Hunter* создаём там *config.cmake*

```
$ mkdir demo
$ cat > demo/main.cpp <<EOF
#include <print.hpp>
#include <cstdlib>

int main(int argc, char* argv[]) {
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr) {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
    return 1;
  }
  std::string text;
  while (std::cin >> text) {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
}
EOF

$ gsed -i -e '/endif()/a\
add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)
' CMakeLists.txt
```

Записали *main.cpp* и добавили в *CMakeLists.txt* некоторые данные

```
$ mkdir tools
$ git submodule add https://github.com/ruslo/polly tools/polly
$ tools/polly/bin/polly.py --test
$ tools/polly/bin/polly.py --install --clear
$ tools/polly/bin/polly.py --toolchain clang-cxx14
```

Добавили *polly* и протестировали через неё
