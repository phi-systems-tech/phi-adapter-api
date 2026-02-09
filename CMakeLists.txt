cmake_minimum_required(VERSION 3.21)

project(phi-adapter-api
    VERSION 1.0.0
    DESCRIPTION "Public header-only adapter API for Phi"
    LANGUAGES CXX
)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

find_package(Qt6 REQUIRED COMPONENTS Core)

add_library(phi_adapter_api INTERFACE)
add_library(phi::adapter-api ALIAS phi_adapter_api)

set_target_properties(phi_adapter_api PROPERTIES
    EXPORT_NAME adapter-api
)

target_compile_features(phi_adapter_api INTERFACE cxx_std_20)

target_link_libraries(phi_adapter_api
    INTERFACE
        Qt6::Core
)

target_include_directories(phi_adapter_api
    INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/phi/adapter/api>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/phi/adapter/api>
)

install(
    DIRECTORY include/phi/adapter/api/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/phi/adapter/api
    FILES_MATCHING PATTERN "*.h"
)

install(
    TARGETS phi_adapter_api
    EXPORT phi-adapter-apiTargets
)

install(
    EXPORT phi-adapter-apiTargets
    FILE phi-adapter-apiTargets.cmake
    NAMESPACE phi::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/phi-adapter-api
)

configure_package_config_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/phi-adapter-apiConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/phi-adapter-apiConfig.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/phi-adapter-api
)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/phi-adapter-apiConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/phi-adapter-apiConfig.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/phi-adapter-apiConfigVersion.cmake
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/phi-adapter-api
)
