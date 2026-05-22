cmake_minimum_required(VERSION 3.12)

project(spify LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

include(GNUInstallDirs)
find_package(Python3 REQUIRED COMPONENTS Interpreter)

include(FetchContent)
FetchContent_Declare(
  yaml-cpp
  URL https://github.com/jbeder/yaml-cpp/archive/refs/tags/yaml-cpp-0.9.0.tar.gz
)
FetchContent_MakeAvailable(yaml-cpp)

function(add_spifyIFP_target IFPName)
  add_custom_command(
    COMMAND ${CMAKE_COMMAND} -E env PYTHONDONTWRITEBYTECODE=1
            PYTHONPATH=${spify_SOURCE_DIR}/src
	    ${Python3_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/generate${IFPName}.py
    DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/generate${IFPName}.py
    OUTPUT ${IFPName}.cpp ${IFPName}.h
    COMMENT "Generating code for ${IFPName}."
  )

  set(targets ${ARGN})
  foreach(target ${targets})
    target_include_directories(${target} PRIVATE ${CMAKE_CURRENT_BINARY_DIR})
    target_sources(${target} PRIVATE ${IFPName}.cpp)
  endforeach()
endfunction()

add_subdirectory(src)
add_subdirectory(test)
add_subdirectory(example)

