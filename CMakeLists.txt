cmake_minimum_required(VERSION 3.3)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

################################################################################

################################################################################
# Project name
################################################################################

project(MeshViewer)

################################################################################
# Settings
################################################################################
if(NOT CMAKE_BUILD_TYPE)
	message(STATUS "No build type selected, default to Release")
	set(CMAKE_BUILD_TYPE "Release")
endif()

set(MESHVIEWER_EXTERNAL "${CMAKE_CURRENT_SOURCE_DIR}/Extern")

# Use folder in Visual Studio
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Export compile flags (used for autocompletion of the C++ code)
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

# Generate position independent code
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

ADD_DEFINITIONS(-DUNICODE)
ADD_DEFINITIONS(-D_UNICODE)

set(LIBIGL_INCLUDE_DIR "./Extern/libigl/include")
set(EIGEN_INCLUDE_DIR "./Extern/Eigen")
set(GLUT_ROOT_DIR "./Extern/freeglut")
set(GLEW_ROOT_DIR "./Extern/glew")
set(GLM_ROOT_DIR "./Extern/glm")

#openmp

FIND_PACKAGE( OpenMP REQUIRED)
if(OPENMP_FOUND)
message("OPENMP FOUND")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
endif()


#link with libigl
include_directories(${LIBIGL_INCLUDE_DIR})

#link with CGAL
find_package(CGAL QUIET)  
  
if ( CGAL_FOUND )  
  
  include( ${CGAL_USE_FILE} )  
  
  include( CGAL_CreateSingleSourceCGALProgram )  
    
  message(STATUS "Find CGAL successfully.")  
  
else()  
    
    message(STATUS "This program requires the CGAL library, and will not be compiled.")  
endif()

#link with opengl
add_subdirectory(${GLUT_ROOT_DIR})
include_directories("${GLUT_ROOT_DIR}/include")
#add_subdirectory(${GLM_ROOT_DIR})
include_directories(${GLM_ROOT_DIR})
add_subdirectory(${GLEW_ROOT_DIR})
include_directories("${GLEW_ROOT_DIR}/include")

#link with Eigen3
INCLUDE_DIRECTORIES(${EIGEN_INCLUDE_DIR})

#link with mosek
find_package(mosek)
INCLUDE_DIRECTORIES(${MOSEK_INCLUDE_DIR})


include_directories ("${PROJECT_SOURCE_DIR}/include")

file(GLOB SRCFILES ./src/*.cpp)
add_executable(${PROJECT_NAME}_bin ${SRCFILES})
target_link_libraries(${PROJECT_NAME}_bin ${CGAL_LIBRARIES} ${CGAL_3RD_PARTY_LIBRARIES} ${OPENGL_gl_LIBRARY}
    freeglut ${MOSEK_LIBRARIES})
target_link_libraries(${PROJECT_NAME}_bin libglew_static)
