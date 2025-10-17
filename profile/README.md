# 🧠 C++ Core Guidelines Cheat Sheet (For Engine Development)

This is a compact reference based on the [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines), tailored for engine or systems-level development.


## Commits and branches

[Conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) can be referred to for guidelines around commit message contents. Commits and pull requests should always be contained and small where possible.


```
Tree structure & naming

|- master (release)
|--|- development (unstable)
|-----|- feat/ 
|--------|- ... (feat/#{ID}-{DESCRIPTION})
|--------|- #01-player-movement
|-----|- fix/
|--------|- ... (fix/#{ID}-{DESCRIPTION})
|--------|- #02-player-sprite

Pull request: PR #{ID} {NAME}
```

### ❗Reviews
* Always assign yourself to the PR you open as contributor.
* A pull request should always be linked to an issue on GitHub.
* A pull request should always be reviewed by at least 1, preferably 2, other(s) developers (not the OP).
* When choosing a reviewer, assign that person to the reviewer through the Discord channel.

---

### 🚀 High-Level Principles

- Express intent clearly — make code self-explanatory.
- Stick to standard ISO C++ — avoid compiler-specific extensions.
- Prefer compile-time safety; if not possible, fail fast at runtime.
- Use RAII and smart pointers to prevent leaks.
- Avoid unnecessary memory or CPU overhead.
- Prefer immutability (`const`) whenever possible.


### 🔌 Interfaces / API Design

- Keep interfaces explicit; avoid hidden state or side effects.
- No mutable globals or singletons.
- Use strong, precise types (avoid `void*` and ambiguous primitives).
- Use `Expects()` / `Ensures()` for pre/postconditions.
- Don’t transfer ownership via raw pointers or references.
- Keep parameters minimal and unambiguous.


### 🏗 Classes, Resources & Ownership

- Use RAII to manage all resources (memory, files, locks, etc.).
- Avoid `new` / `delete` directly — prefer `unique_ptr`, `shared_ptr`, or custom RAII wrappers.
- Define clear ownership semantics — know who “owns” what.
- Minimize coupling between classes and modules.


### 🧮 Expressions, Statements & Logic

- Prefer range-based `for` loops and standard algorithms over manual loops.
- Cache expensive computations — don’t repeat them unnecessarily.
- Avoid implicit narrowing conversions; make them explicit.
- Prefer initialization over assignment.


### 🤡 Error Handling & Safety

- Use exceptions or well-defined error-handling strategies — never ignore failures.
- Validate inputs and invariants early.
- Avoid mixing error codes with exception-based logic.
- Keep error handling consistent across the codebase.


## 🧩 Function & Member Design

### 🧠 General Design

- Keep functions short and focused — one purpose each.
- Use clear, intent-based names (`UpdatePhysics`, `GetVelocity`).
- Avoid side effects unless they’re the main goal.
- Don’t mix initialization and runtime logic.

### ✋ Parameters

- Pass small types by value; large objects by `const&`.
- Use pointers only to indicate optional or nullable parameters.
- Avoid raw pointers for ownership — use smart pointers.
- Inputs first, outputs last.
- Avoid unclear flag parameters — use enums or structs.

### 🔁 Return Values

- Return by value (modern compilers optimize well).
- Use smart pointers only when transferring ownership.
- Never return references or pointers to locals.
- Use `[[nodiscard]]` for functions whose result shouldn’t be ignored.
- Use `optional` when a possible null reference is returned.

### 🧱 Class Members

- Keep data private; expose only what’s needed.
- Use `const` member functions for read-only access.
- Initialize members in initializer lists.
- Mark overrides with `override`; use `final` when appropriate.
- Avoid virtuals unless polymorphism is necessary.
- Default special functions unless custom logic is required.
- Functions, public and private members should be written in `snake_case`
- Private members have an additional `_` suffix

### ⚙ Implementation

- Use `auto` for clarity and maintainability.
- Use `constexpr`, `noexcept`, and `explicit` when suitable.
- Guard invariants with `assert`, `Expects()`, or `Ensures()`.
- Comment only when intent isn’t clear from code.
- Function declarations should always be written in `.cpp` files, unless its a template


# 🧹 Example

```cpp
// .hpp
class Transform {
public:
    Transform(const Vec3& pos = {}, const Vec3& rot = {}, const Vec3& scale = {1,1,1});

    void translate(const Vec3& delta);
    Vec3 get_position() const;

private:
    Vec3 position_, rotation_, scale_;
};

// .cpp
Transform::Transform(const Vec3& pos = {}, const Vec3& rot = {}, const Vec3& scale = {1,1,1})
: position_(pos), rotation_(rot), scale_(scale) {}

void Translate:translate(const Vec3& delta)
{
    position_ = delta;
}

Vec3 Transform::get_position() const
{
    return position_;
}
```

---

# 💾 Global CMakeLists template 

A basic CMakeLists.txt template that utilizes SDL3 and ENet which is cross platform and indepedent.

```cmake
# =========================================================
# Configuration
# =========================================================
cmake_minimum_required(VERSION 3.5)
project(scene-poc)

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
# Box2D (from GitHub, built automatically)
# =========================================================
FetchContent_Declare(
  box2d
  GIT_REPOSITORY https://github.com/erincatto/box2d.git
  GIT_TAG v3.1.1
)
FetchContent_MakeAvailable(box2d)


# =========================================================
# ImGui (Dear ImGui) via FetchContent
# =========================================================
FetchContent_Declare(
  imgui
  GIT_REPOSITORY https://github.com/ocornut/imgui.git
  GIT_TAG docking
)
FetchContent_MakeAvailable(imgui)

# ImGui sources (core + SDL3 + OpenGL3 backends)
set(IMGUI_SOURCES
  ${imgui_SOURCE_DIR}/imgui.cpp
  ${imgui_SOURCE_DIR}/imgui_draw.cpp
  ${imgui_SOURCE_DIR}/imgui_tables.cpp
  ${imgui_SOURCE_DIR}/imgui_widgets.cpp
  ${imgui_SOURCE_DIR}/backends/imgui_impl_sdl3.cpp
  ${imgui_SOURCE_DIR}/backends/imgui_impl_sdlrenderer3.cpp
)

# =========================================================
# Engine library
# =========================================================
file(GLOB_RECURSE ENGINE_SOURCES CONFIGURE_DEPENDS src/*.cpp)

add_library(engine ${ENGINE_SOURCES} ${IMGUI_SOURCES})

target_include_directories(engine
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
    PRIVATE ${imgui_SOURCE_DIR} ${imgui_SOURCE_DIR}/backends
)

target_link_libraries(engine
    PRIVATE ${SDL_MAJOR}::${SDL_MAJOR} enet box2d
)

# =========================================================
# executable
# =========================================================
add_executable(${PROJECT_NAME} src/main.cpp)
target_link_libraries(${PROJECT_NAME} PRIVATE engine ${SDL_MAJOR}::${SDL_MAJOR} enet box2d)

# If your main.cpp needs additional includes for ENet/ImGui:
target_include_directories(${PROJECT_NAME} PRIVATE
    ${enet_SOURCE_DIR}/include
    ${imgui_SOURCE_DIR}
    ${imgui_SOURCE_DIR}/backends
)
```
