# CMakeTemplate A template for CMake projects

# Build system is based on https://dominikberner.ch/cmake-interface-lib/
# https://github.com/bernedom/SI

cmake_minimum_required(VERSION 3.12)

project(
  nigh
  VERSION 0.0.1
  DESCRIPTION "CMake wrap around NIGH"
  LANGUAGES CXX
  HOMEPAGE_URL https://github.com/quimortiz/nigh)


include(GNUInstallDirs)
# Is it required to find eigen here? find_package(Eigen3 REQUIRED)

add_library(nigh INTERFACE)
add_library(nigh::nigh ALIAS nigh)

target_include_directories(
  nigh INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                     $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

target_compile_features(nigh INTERFACE cxx_std_17)


install(
  TARGETS nigh
  EXPORT nigh_Targets
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "nighConfigVersion.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion)

configure_package_config_file(
  "${PROJECT_SOURCE_DIR}/cmake/nighConfig.cmake.in"
  "${PROJECT_BINARY_DIR}/nighConfig.cmake"
  INSTALL_DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/nigh/cmake)

install(
  EXPORT nigh_Targets
  FILE nighTargets.cmake
  NAMESPACE nigh::
  DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/nigh/cmake)

install(FILES "${PROJECT_BINARY_DIR}/nighConfig.cmake"
              "${PROJECT_BINARY_DIR}/nighConfigVersion.cmake"
        DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/nigh/cmake)

install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/nigh DESTINATION include)

if(BUILD_TESTING)
  enable_testing()
  add_subdirectory(test/cpp)
endif()

if(BUILD_PYTHON_BINDINGS)
  add_subdirectory(bindings/python)
endif()
