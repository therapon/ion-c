cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

set(declibsrc
        decContext.c
        decimal128.c
        decimal64.c
        decimal32.c
        decNumber.c
        decPacked.c
        decQuad.c
        )

# this is the "object library" target: compiles the sources only once
add_library(decobjlib OBJECT ${declibsrc})

# shared libraries need PIC
set_property(TARGET decobjlib PROPERTY POSITION_INDEPENDENT_CODE 1)

add_library(decNumber SHARED $<TARGET_OBJECTS:decobjlib>)
add_library(decNumber_static STATIC $<TARGET_OBJECTS:decobjlib>)

target_include_directories(decobjlib
        PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)



set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/DecNumber)

install(TARGETS
            decNumber
            decNumber_static
        EXPORT
            DecNumberTargets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(EXPORT DecNumberTargets
        FILE DecNumberTargets.cmake
        NAMESPACE DecNumber::
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/DecNumber)

install(DIRECTORY include/decNumber
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        FILES_MATCHING PATTERN "*.h")

####
## CMake packaging. Allows other CMake projects to detect and use IonC.
####
include(CMakePackageConfigHelpers)


configure_package_config_file(
        ${CMAKE_SOURCE_DIR}/cmake/DecNumberConfig.cmake.in
        ${CMAKE_BINARY_DIR}/cmake/DecNumberConfig.cmake
        INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/DecNumber)


write_basic_package_version_file(
        ${CMAKE_BINARY_DIR}/cmake/DecNumberConfigVersion.cmake
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY SameMajorVersion)

install(FILES
        ${CMAKE_BINARY_DIR}/cmake/DecNumberConfig.cmake
        ${CMAKE_BINARY_DIR}/cmake/DecNumberConfigVersion.cmake
        DESTINATION
        ${CMAKE_INSTALL_LIBDIR}/cmake/DecNumber)

