cmake_minimum_required(VERSION 3.0)

project(gemmlowp VERSION 1.0.0)

# Define root directory variable for subdirectories
# that will work in case of add_subdirectory() use
set(GEMMLOWP_ROOT_DIR "${CMAKE_CURRENT_LIST_DIR}")

add_library(gemmlowp INTERFACE)
set(gemmlowp_inc_dir "${CMAKE_CURRENT_LIST_DIR}")
target_include_directories(gemmlowp
  INTERFACE
  "$<BUILD_INTERFACE:${gemmlowp_inc_dir}>" # i.e., submodule use
  )

option(GEMMLOWP_BUILD_TESTS "Build test program" OFF)
if(GEMMLOWP_BUILD_TESTS)
  enable_testing()
  add_subdirectory(test)
endif()

### Install ###
set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${version_config}" COMPATIBILITY SameMajorVersion
  )

# Note: use 'targets_export_name'
configure_package_config_file(
  "${PROJECT_SOURCE_DIR}/cmake/Config.cmake.in"
  "${project_config}"
  INSTALL_DESTINATION "${config_install_dir}"
  )

install(
  TARGETS ${PROJECT_NAME}
  EXPORT "${targets_export_name}"
  LIBRARY DESTINATION "lib"
  ARCHIVE DESTINATION "lib"
  RUNTIME DESTINATION "bin"
  INCLUDES DESTINATION "${include_install_dir}"
  )

# install -m 755 -t $(INCLUDE_PREFIX)/public public/*.h
# install -m 755 -t $(INCLUDE_PREFIX)/meta meta/*.h
# install -m 755 -t $(INCLUDE_PREFIX)/internal internal/*.h
# install -m 755 -t $(INCLUDE_PREFIX)/profiling profiling/*.h
# install -m 755 -t $(INCLUDE_PREFIX)/fixedpoint fixedpoint/*.h

foreach(dir public meta internal profiling fixedpoint)
  message("DIR=${dir} ")
  install(
    DIRECTORY ${dir} # No trailing slash
    DESTINATION "${include_install_dir}/"
    FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp"
    )
endforeach()

install(
  FILES "${project_config}" "${version_config}"
  DESTINATION "${config_install_dir}"
  )

install(
  EXPORT "${targets_export_name}"
  NAMESPACE "${namespace}"
  DESTINATION "${config_install_dir}"
  )
