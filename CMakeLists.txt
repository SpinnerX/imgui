cmake_minimum_required(VERSION 3.15)

set(PROJECT_NAME imgui)
project(${PROJECT_NAME} VERSION 1.0)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

set(CMAKE_CXX_VISIBILITY_PRESET hidden)
set(CMAKE_VISIBILITY_INLINES_HIDDEN ON)

option(imgui_BUILD_UNIT_TESTS "Build the imgui unit tests" ON)
option(imgui_BUILD_TESTBED "Build the imgui testbed" ON)
option(imgui_BUILD_DOCS "Build the imgui documentation" OFF)
option(imgui_USER_SETTINGS "Override imgui settings with imguiUserSettings.h" OFF)

option(BUILD_SHARED_LIBS "Build Box2D as a shared library" OFF)

include(GNUInstallDirs)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Finding opengl, glfw, glew, glut, and possibly freetype and even freeglut
if(WIN32)
# add_subdirectory(third_party/glfw)

set(GLFW_ROOT "third_party/glfw")

include_directories("${GLFW_ROOT}\\include")
list(APPEND CMAKE_PREFIX_PATH "${GLFW_ROOT}\\lib-mingw-w64")
# find_package(glfw3 REQUIRED)
else()
find_package(glfw3 REQUIRED)
find_package(OpenGL REQUIRED)
find_package(GLEW REQUIRED)
endif()

if(APPLE)
find_package(GLUT REQUIRED)
find_package(FREEGLUT REQUIRED)
endif(APPLE)

add_subdirectory(src)

if(APPLE)

    # This is making sure we find the SDL libraries on mac
    # include_directories(${SDL2_INCLUDE_DIRS})


    # target_link_libraries(${PROJECT_NAME} pthread)
    # Linking the libraries for Cocoa, OpenGL, IOKit, and glfw
    target_link_libraries(${PROJECT_NAME}
      "-framework Cocoa"
      "-framework OpenGL"
      "-framework IOKit"
      glfw
      ${OPENGL_gl_LIBRARY}
      # GLEW::GLEW
      # ${GLUT_LIBRARIES} # NOTE: If you want to use freeglut, then you need this to link freeglut to get working
      # glad::glad
    )
endif(APPLE)

if(WIN32)
target_link_libraries(${PROJECT_NAME} glfw)
endif(WIN32)

install(
  DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/include/imgui"
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)