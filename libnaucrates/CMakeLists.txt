# Copyright (c) 2015, Pivotal Software, Inc. All Rights Reserved.

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../libgpopt/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../libgpdbcost/include)
# for the generated config.h file.
include_directories(${PROJECT_BINARY_DIR}/libgpos/include/)

file(GLOB_RECURSE hdrs ${CMAKE_CURRENT_SOURCE_DIR}/include/*.h)
file(GLOB_RECURSE srcs ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)

# Add headers to make them visible in some IDEs (Clion, VS, Xcode)
list(APPEND srcs ${hdrs})

add_library(naucrates ${srcs})

target_link_libraries(naucrates
                      ${CMAKE_THREAD_LIBS_INIT} ${CMAKE_DL_LIBS}
                      gpos
                      ${XERCES_LIBRARIES}
                      ${GPORCA_SOLARIS_EXTRA_LIBS})

# Prevent linker errors from undefined symbols on OSX.
if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  set_target_properties(naucrates PROPERTIES
                        LINK_FLAGS "-undefined dynamic_lookup")
endif()

set_target_properties(naucrates PROPERTIES
                      SOVERSION ${GPORCA_ABI_VERSION}
                      VERSION ${GPORCA_VERSION_STRING})

install(TARGETS naucrates DESTINATION lib)
install(DIRECTORY include/naucrates DESTINATION include)
