cmake_minimum_required(VERSION 3.5)
project(rqt_py_common)

# Load catkin and all dependencies required for this package
find_package(ament_cmake REQUIRED)
find_package(ament_cmake_python REQUIRED)

ament_python_install_package(${PROJECT_NAME}
  PACKAGE_DIR src/${PROJECT_NAME})

install(DIRECTORY resource
  DESTINATION share/${PROJECT_NAME}
)

if(BUILD_TESTING)
  find_package(rosidl_default_generators REQUIRED)
  find_package(ament_cmake_pytest REQUIRED)
  # Provides PYTHON_EXECUTABLE_DEBUG
  find_package(python_cmake_module REQUIRED)
  find_package(PythonExtra MODULE)

  if(WIN32 AND CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(${PROJECT_NAME}_PYTHON_EXECUTABLE "${PYTHON_EXECUTABLE_DEBUG}")
  else()
    set(${PROJECT_NAME}_PYTHON_EXECUTABLE "${PYTHON_EXECUTABLE}")
  endif()

  set(PROJECT_NAME "rqt_py_common_test_interfaces")
  rosidl_generate_interfaces(${PROJECT_NAME}
    test/msg/Val.msg
    test/msg/ArrayVal.msg
    test/srv/AddTwoInts.srv
    SKIP_INSTALL)
  # setup fake ament index
  file(MAKE_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/ament_index/share/ament_index/resource_index/rosidl_interfaces/packages")
  file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/ament_index/share/ament_index/resource_index/rosidl_interfaces/packages/${PROJECT_NAME}")
  file(MAKE_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/ament_index/share/ament_index/resource_index/rosidl_interfaces/rosidl_interfaces")
  file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/ament_index/share/ament_index/resource_index/rosidl_interfaces/packages/${PROJECT_NAME}")
  set(PROJECT_NAME "rqt_py_common")

  ament_add_pytest_test(${PROJECT_NAME} test
    APPEND_ENV AMENT_PREFIX_PATH=${CMAKE_CURRENT_BINARY_DIR}/ament_index
    APPEND_ENV PYTHONPATH=${CMAKE_CURRENT_BINARY_DIR}/rosidl_generator_py
    TIMEOUT 90,
    PYTHON_EXECUTABLE "${${PROJECT_NAME}_PYTHON_EXECUTABLE}")
endif()

ament_package()
