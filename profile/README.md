# 💾 Global CMakeLists template 

A basic CMakeLists template that utilizes SDL3 and enet which is cross platform and indepedent.

```cmake
cmake_minimum_required(VERSION 3.24)
project(<PROJECTNAME>)

set(CMAKE_CXX_STANDARD 20)

include(FetchContent)

# =========================================================
# SDL3 (prebuilt binaries from official release)
# =========================================================
set(SDL3_VERSION 3.2.22)

FetchContent_Declare(
  SDL3_prebuilt
  GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
  GIT_TAG release-${SDL3_VERSION}
)
FetchContent_MakeAvailable(SDL3_prebuilt)

# SDL3 root (unzipped package looks like SDL3-3.2.22/)
set(SDL3_ROOT ${SDL3_prebuilt_SOURCE_DIR}/SDL3-${SDL3_VERSION})

# Add includes and libs
include_directories(${SDL3_ROOT}/include)
link_directories(${SDL3_ROOT}/lib)

# =========================================================
# ENet (from GitHub, built automatically)
# =========================================================
FetchContent_Declare(
  ENet
  GIT_REPOSITORY https://github.com/lsalzman/enet.git
  GIT_TAG master
)
FetchContent_MakeAvailable(ENet)

# =========================================================
# Executable
# =========================================================
file(GLOB_RECURSE SOURCES src/*.cpp)
file(GLOB_RECURSE HEADERS src/*.h)
add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

# Link SDL3 and ENet
target_link_libraries(${PROJECT_NAME} PRIVATE SDL3 enet)
```
---
