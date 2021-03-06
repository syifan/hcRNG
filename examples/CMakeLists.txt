#Object libraries require CMAKE 2.8.8 version 
CMAKE_MINIMUM_REQUIRED(VERSION 2.8.8) 
MESSAGE(STATUS "CMAKE VERSION ${CMAKE_VERSION}")

SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/../cmake ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
# Find HCC compiler
FIND_PACKAGE(HC++ 1.0 REQUIRED)

FILE(GLOB SRCS *.cpp)

EXECUTE_PROCESS(COMMAND ${HCC_CONFIG} --install --cxxflags
                            OUTPUT_VARIABLE HCC_CXXFLAGS)
EXECUTE_PROCESS(COMMAND ${HCC_CONFIG}  --install --ldflags
                            OUTPUT_VARIABLE HCC_LDFLAGS)

SET(HCBLAS_INCLUDE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../lib/include/")
SET(HCBLAS_LIBRARY_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../build/lib/src")

STRING(STRIP "${HCC_CXXFLAGS}" HCC_CXXFLAGS)
SET(HCC_CXXFLAGS "${HCC_CXXFLAGS} -I${HCBLAS_INCLUDE_PATH}")
STRING(STRIP "${HCC_LDFLAGS}" HCC_LDFLAGS)
SET(HCC_LDFLAGS "${HCC_LDFLAGS} -L${HCBLAS_LIBRARY_PATH}")
SET(LINK "-lhcrng -lhc_am")

SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY "bin/")
FOREACH(example_file ${SRCS})
  SET_PROPERTY(SOURCE ${example_file} APPEND_STRING PROPERTY COMPILE_FLAGS " ${HCC_CXXFLAGS} -DGTEST_HAS_TR1_TUPLE=0")
  GET_FILENAME_COMPONENT(name_without_extension ${example_file} NAME_WE)
  ADD_EXECUTABLE(${name_without_extension} ${example_file})
  SET_PROPERTY(TARGET ${name_without_extension} APPEND_STRING PROPERTY LINK_FLAGS " ${HCC_LDFLAGS} ${LINK}")
ENDFOREACH()
