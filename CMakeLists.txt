cmake_minimum_required(VERSION 3.1)

set(CMAKE_CXX_STANDARD 17)

project(sare-init-visulizer)

add_subdirectory("dep/glfw")
add_subdirectory("dep/glad")
add_subdirectory("dep/glm")
set(ASSIMP_BUILD_ALL_IMPORTERS_BY_DEFAULT OFF CACHE BOOL " " FORCE)
set(ASSIMP_BUILD_OBJ_IMPORTER ON  CACHE BOOL " " FORCE)
set(ASSIMP_BUILD_PLY_IMPORTER ON  CACHE BOOL " " FORCE)
set(ASSIMP_NO_EXPORT          ON  CACHE BOOL " " FORCE)
set(ASSIMP_BUILD_TESTS        OFF CACHE BOOL " " FORCE)
set(ASSIMP_BUILD_ASSIMP_TOOLS OFF CACHE BOOL " " FORCE)
set(ASSIMP_INSTALL            OFF CACHE BOOL " " FORCE)
set(BUILD_SHARED_LIBS         OFF CACHE BOOL " " FORCE)
add_subdirectory("dep/assimp")
add_subdirectory("dep/lua")
add_subdirectory("dep/googletest")

set(PROJECT_SOURCES
  src/main.cpp
  src/Visualizer.cpp
  src/Logger.cpp
  src/Input.cpp
  src/SceneObject.cpp
  src/Camera.cpp
  src/MeshObject.cpp
  src/MeshBuilder.cpp
  src/VisualizerScene.cpp
  src/Mesh.cpp
  src/stb_image.cpp
  src/LightObject.cpp
  src/Framebuffer.cpp
  src/LuaIndexable.cpp

  src/shaders/Shader.cpp
  src/shaders/PhongShader.cpp
  src/shaders/Compositor.cpp

  src/animation/Smoother.cpp
  src/animation/Easing.cpp
  src/animation/Track.cpp
  src/animation/Timeline.cpp
)

set(PROJECT_INCLUDES
  include
  dep/glfw/include
  dep/glad/include
  dep/glm
)

set(PROJECT_LIBRARIES
  glfw
  glad
  glm
  assimp
  lua
)

add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})

target_include_directories(${PROJECT_NAME} PRIVATE ${PROJECT_INCLUDES})

target_link_libraries(${PROJECT_NAME} ${PROJECT_LIBRARIES})

add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy_directory
  ${CMAKE_CURRENT_SOURCE_DIR}/res
  ${CMAKE_CURRENT_BINARY_DIR}/res
  COMMENT "Copying resources to build directory"
)

enable_testing()

set(TESTER ${PROJECT_NAME}-test)

list(REMOVE_ITEM PROJECT_SOURCES src/main.cpp)

add_executable(${TESTER}
  ${PROJECT_SOURCES}

  tests/test.cpp
  tests/luatests.cpp
  tests/animationtests.cpp
)

target_include_directories(${TESTER} PRIVATE ${PROJECT_INCLUDES})

target_link_libraries(${TESTER}
  ${PROJECT_LIBRARIES}

  gtest
)

add_test(NAME ${TESTER} COMMAND ${TESTER})

add_custom_command(TARGET ${TESTER} POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy_directory
  ${CMAKE_CURRENT_SOURCE_DIR}/res
  ${CMAKE_CURRENT_BINARY_DIR}/res
  COMMENT "Copying resources to build directory"
)