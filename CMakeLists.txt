cmake_minimum_required(VERSION 3.20)
project(gmsh_tools VERSION 1.0 LANGUAGES CXX)

set(PROJECT_IS_TOP_LEVEL OFF)
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
    set(PROJECT_IS_TOP_LEVEL ON)
endif()

option(BUILD_GMSH_TOOLS_EXECUTABLE "Build the gmsh_tools executable" ${PROJECT_IS_TOP_LEVEL})

set(LIB_NAME gmshtools)
set(EXE_NAME ${PROJECT_NAME})

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(LIB_SOURCES src/gmshData.cpp src/gmshElement.cpp src/gmshMesh.cpp)

add_library(${LIB_NAME} STATIC ${LIB_SOURCES})

target_include_directories(${LIB_NAME}
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
)

if(BUILD_GMSH_TOOLS_EXECUTABLE)
    add_executable(${EXE_NAME} src/main.cc)

    target_link_libraries(${EXE_NAME}
        PRIVATE ${LIB_NAME}
    )
endif()


include(GNUInstallDirs)

install(TARGETS ${LIB_NAME}
    EXPORT ${LIB_NAME}Targets
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(DIRECTORY include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

if(BUILD_EXECUTABLE)
    install(TARGETS ${EXE_NAME}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    )
endif()

install(EXPORT ${LIB_NAME}Targets
    FILE ${LIB_NAME}Targets.cmake
    NAMESPACE ${LIB_NAME}::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${LIB_NAME}
)

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/cmake/${LIB_NAME}Config.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/cmake/${LIB_NAME}Config.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${LIB_NAME}
)
