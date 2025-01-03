cmake_minimum_required(VERSION 3.30)
project(TestBGFX)

set(CMAKE_CXX_STANDARD 20)

if( ${CMAKE_SYSTEM_NAME} MATCHES "Emscripten")
    set(USE_FLAGS "-s USE_SDL=2")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${USE_FLAGS}")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${USE_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${USE_FLAGS}")
    set(CMAKE_EXECUTABLE_SUFFIX .html)
else()
    find_package(SDL2 REQUIRED)
endif()

set(BX_AMALGAMATED ON)
set(BGFX_AMALGAMATED ON)
set(BGFX_BUILD_EXAMPLES OFF)

add_subdirectory(third-party)

bgfx_compile_shaders(
        TYPE VERTEX
        SHADERS shader/v_simple.sc
        VARYING_DEF ${CMAKE_SOURCE_DIR}/shader/varying.def.sc
        OUTPUT_DIR ${CMAKE_BINARY_DIR}/include/generated/shaders
        AS_HEADERS
)

bgfx_compile_shaders(
        TYPE FRAGMENT
        SHADERS shader/f_simple.sc
        VARYING_DEF ${CMAKE_SOURCE_DIR}/shader/varying.def.sc
        OUTPUT_DIR ${CMAKE_BINARY_DIR}/include/generated/shaders
        AS_HEADERS
)
add_executable(TestBGFX main.cpp shader/v_simple.sc  shader/f_simple.sc)
if( ${CMAKE_SYSTEM_NAME} MATCHES "Emscripten")
else()
    target_link_libraries(TestBGFX SDL2::SDL2)
endif()
target_link_libraries(TestBGFX bx bgfx)

target_include_directories(TestBGFX PUBLIC ${CMAKE_BINARY_DIR}/include/generated/shaders)

