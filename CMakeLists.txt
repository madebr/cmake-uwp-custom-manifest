set(CMAKE_SYSTEM_NAME    "WindowsStore")
set(CMAKE_SYSTEM_VERSION "10.0")

cmake_minimum_required(VERSION 3.26)
project(uwp_test CXX)

set(number_targets "2" CACHE STRING "Number of targets to build")
set(i 0)
while(i LESS "${number_targets}")
  set(TARGET "target${i}")
  #set(target_dir "${CMAKE_CURRENT_BINARY_DIR}/${TARGET}.dir")
  configure_file(Package.appxmanifest.in "${TARGET}.appxmanifest" @ONLY)
  add_executable("${TARGET}"
    main.cpp
    "${CMAKE_CURRENT_BINARY_DIR}/${TARGET}.appxmanifest"
    "logo.png"
    "splash.png"
    "square-44x44.png"
    "square-150x150.png"
  )
  target_compile_options("${TARGET}" PRIVATE "/ZW")
  math(EXPR i "${i} + 1")
endwhile()
