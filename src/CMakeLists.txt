set(
    all_src
    imgui_demo.cpp
    imgui_tables.cpp
    imgui.cpp
    imgui_draw.cpp
    imgui_widgets.cpp
    backends/imgui_impl_glfw.cpp
    backends/imgui_impl_opengl3.cpp
	backends/imgui_impl_vulkan.cpp
)

set(
  all_headers
  ../include/imgui/imconfig.h
  ../include/imgui/imgui_internal.h
  ../include/imgui/imgui.h
  ../include/imgui/imstb_rectpack.h
  ../include/imgui/imstb_textedit.h
  ../include/imgui/imstb_truetype.h

  ../include/imgui/backends/imgui_impl_glfw.h
  ../include/imgui/backends/imgui_impl_opengl2.h
  ../include/imgui/backends/imgui_impl_opengl3_loader.h
  ../include/imgui/backends/imgui_impl_opengl3.h
  ../include/imgui/backends/imgui_impl_vulkan.h
)

add_library(imgui ${all_src} ${all_headers})



target_include_directories(imgui
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
)


set_target_properties(imgui PROPERTIES
	CXX_STANDARD 20
    CXX_STANDARD_REQUIRED YES
    CXX_EXTENSIONS NO
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
)

if(MSVC)
  set_target_properties(imgui PROPERTIES
    COMPILE_PDB_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}"
    COMPILE_PDB_NAME imgui
  )

  install(
    FILES "${CMAKE_CURRENT_BINARY_DIR}/$<CONFIG>/imgui.pdb"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}
    OPTIONAL
  )
endif()

if (BUILD_SHARED_LIBS)
  target_compile_definitions(imgui
    PUBLIC
      imgui_SHARED
  )
endif()

source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}" PREFIX "src" FILES ${all_src})
source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/../include" PREFIX "include" FILES ${all_headers})

install(
  TARGETS imgui
  EXPORT imguiConfig
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

install(
  EXPORT imguiConfig
  NAMESPACE imgui::
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/imgui"
)

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/imguiConfigVersion.cmake"
  COMPATIBILITY SameMajorVersion
)

install(
  FILES "${CMAKE_CURRENT_BINARY_DIR}/imguiConfigVersion.cmake"
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/imgui"
)

