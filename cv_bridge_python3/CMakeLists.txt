cmake_minimum_required(VERSION 2.8)
project(cv_bridge_python3)

set(Python_ADDITIONAL_VERSIONS 3.6;3.7;3.8) ## https://stackoverflow.com/questions/29245558/how-to-link-with-python3-libs-with-cmake
find_package(PythonInterp 3 REQUIRED) ## cv_bridge_python3 :: use Python3 by default
find_package(PythonLibs "${PYTHON_VERSION_MAJOR}.${PYTHON_VERSION_MINOR}")

find_package(catkin REQUIRED COMPONENTS rosconsole sensor_msgs)

if(NOT ANDROID)
  find_package(PythonLibs)
  if(PYTHONLIBS_VERSION_STRING VERSION_LESS 3)
    find_package(Boost REQUIRED python)
  else()
    find_package(Boost REQUIRED python3)
  endif()
else()
find_package(Boost REQUIRED)
endif()
find_package(OpenCV 3 REQUIRED
  COMPONENTS
    opencv_core
    opencv_imgproc
    opencv_imgcodecs
  CONFIG
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS rosconsole sensor_msgs
  DEPENDS OpenCV
## cv_bridge_pytyhon3 :: does not install cmakelibrary,
#  CFG_EXTRAS cv_bridge-extras.cmake
)

catkin_python_setup()

include_directories(include ${Boost_INCLUDE_DIRS} ${OpenCV_INCLUDE_DIRS} ${catkin_INCLUDE_DIRS})

## cv_bridge_pytyhon3 :: re-set PROJECT_NAME to cv_bridge
set(PROJECT_NAME cv_bridge)
## cv_bridge_pytyhon3 :: re-set CATKIN_PACKAGE_PYTHON_DESTINATION with PROJECT_NAME as cv_bridge
set(CATKIN_PACKAGE_PYTHON_DESTINATION lib/python${PYTHON_VERSION_MAJOR}/dist-packages/${PROJECT_NAME})

if(NOT ANDROID)
add_subdirectory(python)
endif()
add_subdirectory(src)
if(CATKIN_ENABLE_TESTING)
  add_subdirectory(test)
endif()

# install the include folder
## cv_bridge_python3 :: we can share include/cv_bridge
# install(
#   DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
# )
