cmake_minimum_required(VERSION 3.14)
project(EAIK VERSION 1.2.2 LANGUAGES CXX)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE Release)
endif()

find_package(Eigen3 3.3 REQUIRED NO_MODULE)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

set(IK_GEO_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../external/ik-geo/cpp/subproblems)

add_library(EAIK
    EAIK.cpp
    utils/kinematic_remodeling.cpp
    IK/utils/kinematic_utils.cpp
    IK/1R_IK.cpp
    IK/2R_IK.cpp
    IK/3R_IK.cpp
    IK/4R_IK.cpp
    IK/5R_IK.cpp
    IK/6R_IK.cpp
    ${IK_GEO_DIR}/sp.cpp
)
add_library(EAIK::EAIK ALIAS EAIK)
target_compile_features(EAIK PUBLIC cxx_std_17)
set_target_properties(EAIK PROPERTIES POSITION_INDEPENDENT_CODE ON)
target_include_directories(EAIK PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/IK>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/IK/utils>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/utils>
    $<BUILD_INTERFACE:${IK_GEO_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/eaik>
)
target_link_libraries(EAIK PUBLIC Eigen3::Eigen)

install(TARGETS EAIK EXPORT EAIKTargets
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
install(FILES EAIK.h IK/IKS.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/eaik)
install(EXPORT EAIKTargets NAMESPACE EAIK:: DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/EAIK)

configure_package_config_file(EAIKConfig.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/EAIKConfig.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/EAIK
)
write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/EAIKConfigVersion.cmake
    COMPATIBILITY SameMajorVersion
)
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/EAIKConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/EAIKConfigVersion.cmake
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/EAIK
)
