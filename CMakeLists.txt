# References:
# https://github.com/raysan5/raylib-game-template/blob/main/CMakeLists.txt
# https://github.com/seletz/raylib-hot-code-reload-c-example/blob/develop/CMakeLists.txt
cmake_minimum_required(VERSION 3.30)
set(CMAKE_BUILD_TYPE Debug)

project(
  geomages
  VERSION 0.1
  DESCRIPTION "Game by Willis Plummer"
  LANGUAGES C)

# Deps
# Raylib

include(FetchContent)
set(FETCHCONTENT_QUIET FALSE)

set(RAYLIB_VERSION 5.0)

FetchContent_Declare(
    raylib
    DOWNLOAD_EXTRACT_TIMESTAMP OFF
    URL https://github.com/raysan5/raylib/archive/refs/tags/${RAYLIB_VERSION}.tar.gz
    FIND_PACKAGE_ARGS ${RAYLIB_VERSION} EXACT
)

set(BUILD_EXAMPLES OFF CACHE BOOL "" FORCE) # don't build the supplied examples
set(BUILD_GAMES    OFF CACHE BOOL "" FORCE) # don't build the supplied example games
set(BUILD_SHARED_LIBS ON CACHE BOOL "" FORCE)

# Only populate if not already done
if(NOT raylib_POPULATED)
    FetchContent_MakeAvailable(raylib)
endif()

# My Game

add_library(gamecode MODULE src/game.c src/game.h)
target_link_libraries(gamecode raylib)

add_executable(${PROJECT_NAME} src/main.c src/reload.c)
target_link_libraries(${PROJECT_NAME} raylib)

if(APPLE)
    target_link_libraries(${PROJECT_NAME} "-framework IOKit")
    target_link_libraries(${PROJECT_NAME} "-framework Cocoa")
    target_link_libraries(${PROJECT_NAME} "-framework OpenGL")
endif()

add_custom_target(run
    COMMAND bash -c './${PROJECT_NAME}'
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)

add_custom_target(watch
    COMMAND ${CMAKE_COMMAND} -E echo "Watching for changes using fswatch..."
    COMMAND bash -c './${PROJECT_NAME} & fswatch -0 -o ../src | xargs -0 -n1 -I"{}" make'
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)



