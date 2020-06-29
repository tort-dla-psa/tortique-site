project(emsite)
cmake_minimum_required(VERSION 3.5.1)

option(USE_FS "use filesystem" OFF)

add_definitions(-std=c++17 -O3 -s -march=native -mtune=native)
include_directories(third-party/imgui
    third-party/imgui/examples
    )

file(GLOB SOURCES *.cpp)
set(SOURCES ${SOURCES}
    third-party/imgui/imgui.cpp
    third-party/imgui/imgui_draw.cpp
    third-party/imgui/imgui_widgets.cpp
    third-party/imgui/imgui_demo.cpp
    third-party/imgui/examples/imgui_impl_opengl3.cpp
    third-party/imgui/examples/imgui_impl_sdl.cpp
    )

set(CMAKE_EXECUTABLE_SUFFIX ".html")
set(EMS_FLAGS "-s USE_SDL=2 -s USE_SDL_IMAGE=2\
    -s WASM=1\
    -s ALLOW_MEMORY_GROWTH=1\
    -s DISABLE_EXCEPTION_CATCHING=1 -s NO_EXIT_RUNTIME=0\
    -s ASSERTIONS=1\
    -o ${PROJECT_NAME}.html\
    -s EXPORTED_FUNCTIONS='[_main]'")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${EMS_FLAGS}")

if(USE_FS)
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS}\
        --no-heap-copy --preload-file misc/fonts@/fonts\
        ")
else()
    set(EMS_FLAGS "${EMS_FLAGS}\
        -s NO_FILESYSTEM=1 -DIMGUI_DISABLE_FILE_FUNCTIONS")
    add_definitions(-DIMGUI_DISABLE_FILE_FUNCTIONS)
endif()

add_executable(${PROJECT_NAME} ${SOURCES})
target_link_libraries(${PROJECT_NAME} ${EMS_FLAGS})
set_target_properties(${PROJECT_NAME} PROPERTIES LINK_FLAGS "--shell-file ${PROJECT_SOURCE_DIR}/shell_minimal.html")
