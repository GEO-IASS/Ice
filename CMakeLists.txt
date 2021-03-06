PROJECT(Ice)

cmake_minimum_required(VERSION 2.6)

set(Ice_VERSION_MAJOR "0")
set(Ice_VERSION_MINOR "4")
set(Ice_VERSION_PATCH "0")

set(CMAKE_MODULE_PATH
    ${CMAKE_SOURCE_DIR}/CMake
    ${CMAKE_MODULE_PATH})

include(CTest)

#----------------------------------------------------------------------------
# ensure WIN32 definition
if(WIN32)
  add_definitions(-DWIN32)
endif(WIN32)

#----------------------------------------------------------------------------
#Prevents conflicts with visual min/max function
if(WIN32)

  add_definitions(-DNOMINMAX)

  add_definitions(
    -D_CRT_FAR_MAPPINGS_NO_DEPRECATE
    -D_CRT_IS_WCTYPE_NO_DEPRECATE
    -D_CRT_MANAGED_FP_NO_DEPRECATE
    -D_CRT_NONSTDC_NO_DEPRECATE
    -D_CRT_SECURE_NO_DEPRECATE
    -D_CRT_SECURE_NO_DEPRECATE_GLOBALS
    -D_CRT_SETERRORMODE_BEEP_SLEEP_NO_DEPRECATE
    -D_CRT_TIME_FUNCTIONS_NO_DEPRECATE
    -D_CRT_VCCLRIT_NO_DEPRECATE
    -D_SCL_SECURE_NO_DEPRECATE
  )

  if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    add_definitions(-D_CRT_NONSTDC_NO_WARNING)
  endif()

endif(WIN32)

OPTION(BUILD_ICE_APPLICATION "Build the Ice application (requires glfw and glut)" ON)

# include before OTB includes in case ice is already installed in the system (at otb install location)
include_directories(Code)

#----------------------------------------------------------------------------
# ITK
# find_package(ITK REQUIRED)
# if(ITK_FOUND)
#   MESSAGE(STATUS "ITK found")
#   include(${ITK_USE_FILE})
# else(ITK_FOUND)
#   message(FATAL_ERROR "ITK not found. Please set ITK_DIR")
# endif(ITK_FOUND)


#----------------------------------------------------------------------------
# Orfeo ToolBox
find_package(OTB REQUIRED
  COMPONENTS OTBImageIO OTBVectorDataIO OTBProjection OTBStatistics)
if(OTB_FOUND)
  MESSAGE(STATUS "OTB found")
  include(${OTB_USE_FILE})
else(OTB_FOUND)
  message(FATAL_ERROR "OTB not found. Please set OTB_DIR")
endif(OTB_FOUND)

#----------------------------------------------------------------------------
# OpenGL (needed by Qt4)
find_package( OpenGL REQUIRED )

if (OPENGL_FOUND)
  MESSAGE(STATUS "OpenGL found")
  include_directories(${OPENGL_INCLUDE_DIR})
 else (OPENGL_FOUND)
    MESSAGE(FATAL_ERROR "OpenGL missing")
 endif (OPENGL_FOUND)

find_package(GLEW REQUIRED)

if(GLEW_FOUND)
  MESSAGE(STATUS "Glew found")
  include_directories(${GLEW_INCLUDE_PATH})
else(GLEW_FOUND)
  MESSAGE(FATAL_ERROR "Glew missing. Please set glew directories.")
endif(GLEW_FOUND)

if(BUILD_ICE_APPLICATION)

find_package(GLFW REQUIRED)

if(GLFW_FOUND)
  MESSAGE(STATUS "GLFW found")
  include_directories(${GLFW_INCLUDE_DIR})
else(GLFW_FOUND)
  MESSAGE(FATAL_ERROR "GLFW missing. Please set glfw directories or deactivate the BUILD_ICE_APPLICATION option.")
endif(GLFW_FOUND)

find_package(GLUT REQUIRED)

if(GLUT_FOUND)
MESSAGE(STATUS "Glut found")
include_directories(${GLUT_INCLUDE_DIR})
else(GLUT_FOUND)
MESSAGE(FATAL_ERROR "Glut missing. Please set glut directories or deactivate the BUILD_ICE_APPLICATION option.")
endif(GLUT_FOUND)

endif(BUILD_ICE_APPLICATION)

SET(BUILD_SHARED_LIBS ${OTB_BUILD_SHARED})

set(Ice_LIBRARY_PROPERTIES
    VERSION   "${Ice_VERSION_MAJOR}.${Ice_VERSION_MINOR}.${Ice_VERSION_PATCH}"
    SOVERSION "${Ice_VERSION_MAJOR}.${Ice_VERSION_MINOR}"
)

# Output directories
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${Ice_BINARY_DIR}/bin CACHE INTERNAL "Single output directory for all ARCHIVE products (static libs, import libs)")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${Ice_BINARY_DIR}/bin CACHE INTERNAL "Single output directory for all LIBRARY products (so, modules)")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${Ice_BINARY_DIR}/bin CACHE INTERNAL "Single output directory for all RUNTIME products (executables, dlls)")

# Install dirs
if(NOT Ice_INSTALL_BIN_DIR)
  set(Ice_INSTALL_BIN_DIR "bin")
endif(NOT Ice_INSTALL_BIN_DIR)

if(NOT Ice_INSTALL_LIB_DIR)
  set(Ice_INSTALL_LIB_DIR "lib/")
endif(NOT Ice_INSTALL_LIB_DIR)

if(NOT Ice_INSTALL_INCLUDE_DIR)
  set(Ice_INSTALL_INCLUDE_DIR "include/")
endif(NOT Ice_INSTALL_INCLUDE_DIR)

add_subdirectory(Code)

if(BUILD_ICE_APPLICATION)
add_subdirectory(Application)
endif(BUILD_ICE_APPLICATION)
