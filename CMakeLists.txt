cmake_minimum_required(VERSION 2.8.3)
project(hrp4c_description)

option(DISABLE_ROS OFF)
find_package(catkin QUIET)

if(DISABLE_ROS)
  message(STATUS "Building without ROS support as DISABLE_ROS=ON")
endif()

if(NOT DISABLE_ROS AND ${catkin_FOUND}) # build with catkin
  catkin_package(SKIP_PKG_CONFIG_GENERATION)

  # Configure the develspace pkg-config file
  set(DATADIR "${PROJECT_SOURCE_DIR}")
  configure_file(
    "${PROJECT_SOURCE_DIR}/pkgconfig.pc.cmake"
    "${CATKIN_DEVEL_PREFIX}/lib/pkgconfig/${PROJECT_NAME}.pc")
else() # catkin-free build
  include(CMakePackageConfigHelpers)
  set(VERSION_CONFIG "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
  set(PROJECT_CONFIG "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake")
  set(CONFIG_INSTALL_DIR "lib/cmake/${PROJECT_NAME}")
  write_basic_package_version_file(
    ${VERSION_CONFIG}
    VERSION 1.0.0
    COMPATIBILITY SameMajorVersion
  )
  configure_package_config_file(cmake/Config.cmake.in "${PROJECT_CONFIG}" INSTALL_DESTINATION "${CONFIG_INSTALL_DIR}")
  install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake" "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake" DESTINATION "${CONFIG_INSTALL_DIR}")
  set(CATKIN_PACKAGE_SHARE_DESTINATION share/${PROJECT_NAME})
endif()

# Configure the installspace pkg-config file
set(DATADIR "${CMAKE_INSTALL_PREFIX}/${CATKIN_PACKAGE_SHARE_DESTINATION}")
file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/catkin_generated/installspace)
configure_file(
  "${PROJECT_SOURCE_DIR}/pkgconfig.pc.cmake"
  "${CMAKE_CURRENT_BINARY_DIR}/catkin_generated/installspace/${PROJECT_NAME}.pc")
install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/catkin_generated/installspace/${PROJECT_NAME}.pc
  DESTINATION lib/pkgconfig)

foreach(dir convex launch meshes rsdf urdf)
   install(DIRECTORY ${dir}/
      DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/${dir})
endforeach(dir)
