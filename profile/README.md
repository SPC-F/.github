# 💾 Global CMakeLists template 

A basic CMakeLists.txt template that utilizes SDL3 and ENet which is cross platform and indepedent.

```cmake
# =========================================================
# Configuration
# =========================================================
cmake_minimum_required(VERSION 3.5)
project(<PROJECT_NAME>)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

include(FetchContent)

# =========================================================
# SDL3
# =========================================================
set(SDL_MAJOR SDL3)
set(SDL_VERSION 3.2.22)

FetchContent_Declare(
    ${SDL_MAJOR}
    URL https://github.com/libsdl-org/SDL/releases/download/release-${SDL_VERSION}/${SDL_MAJOR}-${SDL_VERSION}.tar.gz
)

FetchContent_MakeAvailable(${SDL_MAJOR})

# =========================================================
# ENet
# =========================================================
set(ENET_VERSION 1.3.18)

FetchContent_Declare(
    ENet
    URL http://enet.bespin.org/download/enet-${ENET_VERSION}.tar.gz
)

FetchContent_MakeAvailable(ENet)

# =========================================================
# Executable
# =========================================================
file(GLOB_RECURSE SOURCES src/*.cpp)
file(GLOB_RECURSE HEADERS src/*.h)

add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

target_link_libraries(${PROJECT_NAME} PRIVATE ${SDL_MAJOR}::${SDL_MAJOR} enet)

```
