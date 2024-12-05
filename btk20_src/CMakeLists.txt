cmake_minimum_required(VERSION 3.26)
project(btk20)

set(CMAKE_INSTALL_PREFIX /home/hhquan/anaconda3/envs/btk)

# Setting installation directory
set(CMAKE_INSTALL_INCLUDEDIR "include" CACHE STRING
                         "Installation directory for headers")
set(CMAKE_INSTALL_LIBDIR "lib" CACHE STRING
                         "Installation directory for library")
set(TARGET_PYTHON_VERSION "2.7" CACHE STRING
                          "python version to be used")
set(PYTHON_SITE_PACKAGES "lib/python${TARGET_PYTHON_VERSION}/site-packages/${PROJECT_NAME}")

message(STATUS "Setting installation directory...")
message(STATUS "   Include: ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_INCLUDEDIR}")
message(STATUS "   Lib:     ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
message(STATUS "   Python:  ${CMAKE_INSTALL_PREFIX}/${PYTHON_SITE_PACKAGES}")

set(CMAKE_C_FLAGS "-Wall -fPIC")
set(CMAKE_C_FLAGS_RELEASE "-O3")
set(CMAKE_C_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS "-Wall -fPIC")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")
set(CMAKE_CXX_FLAGS_DEBUG "-g")
# print verbose information for memory debugging
# add_definitions(-DDEBUG_MEMORY_MANAGER)
add_definitions(-DENABLE_LEGACY_BTK_API)
add_definitions(-DNPY_NO_DEPRECATED_API)

# For swig
find_package(SWIG 4.0 COMPONENTS python)
if(SWIG_FOUND)
    message("SWIG found: ${SWIG_EXECUTABLE}")
    if(NOT SWIG_fortran_FOUND)
        message(WARNING "SWIG Fortran bindings cannot be generated")
    endif()
endif()
include(${SWIG_USE_FILE})
set(CMAKE_SWIG_FLAGS "-I${CMAKE_SOURCE_DIR}" "-I${CMAKE_SOURCE_DIR}/include")

# For python binding 3.18高版本有用
find_package(Python COMPONENTS Interpreter Development)

message(STATUS "python = " ${Python_FOUND})
message(STATUS "python = " ${Python_INCLUDE_DIRS})
message(STATUS "python = " ${Python_LIBRARY_DIRS})
message(STATUS "python = " ${Python_LIBRARIES})

message(STATUS "numpy " ${Python_NumPy_FOUND})
message(STATUS "numpy " ${Python_NumPy_INCLUDE_DIRS})
message(STATUS "numpy " ${Python_NumPy_VERSION})

# numpy的头文件
execute_process (
    COMMAND ${Python_EXECUTABLE} -c import\ numpy\;\ print\(numpy.get_include\(\)\)\;
    ERROR_VARIABLE NUMPY_FIND_ERROR
    RESULT_VARIABLE NUMPY_FIND_RESULT
    OUTPUT_VARIABLE NUMPY_FIND_OUTPUT
    OUTPUT_STRIP_TRAILING_WHITESPACE
)
message(STATUS "numpy = " ${NUMPY_FIND_OUTPUT})
set(NUMPY_INCLUDES ${NUMPY_FIND_OUTPUT})

# numpy的版本
if (Python_FOUND)
    execute_process (
        COMMAND ${Python_EXECUTABLE} -c import\ numpy\;\ print\(numpy.__version__\)\;
        ERROR_VARIABLE NUMPY_API_VERSION_ERROR
        RESULT_VARIABLE NUMPY_API_VERSION_RESULT
        OUTPUT_VARIABLE NUMPY_API_VERSION
        OUTPUT_STRIP_TRAILING_WHITESPACE
    )
endif()
message(STATUS "NUMPY_API_VERSION = " ${NUMPY_API_VERSION})

# numpy的库文件
message(STATUS "site package = " ${PYTHON_SITE_PACKAGES})
unset(NUMPY_LIBRARIES)
if (PYTHON_SITE_PACKAGES)
    find_library(NUMPY_NPYMATH_LIBRARY NAMES libnpymath.a
        HINTS /home/hhquan/anaconda3/envs/btk/lib/python2.7/site-packages/numpy/core/lib        
    )
    if (NUMPY_NPYMATH_LIBRARY)
            list (APPEND NUMPY_LIBRARIES ${NUMPY_NPYMATH_LIBRARY})
    endif()
endif()

message (STATUS "NUMPY_INCLUDES    = " ${NUMPY_INCLUDES})
message (STATUS "NUMPY_LIBRARIES   = " ${NUMPY_LIBRARIES})
message (STATUS "NUMPY_API_VERSION = " ${NUMPY_API_VERSION})

# sndfile库
include_directories(/home/hhquan/anaconda3/envs/tuya/include)
link_directories(/home/hhquan/anaconda3/envs/tuya/lib)

# GSL库
set(GSL_INCLUDE_DIRS ${PROJECT_SOURCE_DIR}/gsl_release/include)
link_directories(${PROJECT_SOURCE_DIR}/gsl_release/lib)
set(GSL_LIBRARIES gsl)
set(GSL_CBLAS_LIBRARY gslcblas)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_SOURCE_DIR})

include_directories(${Python_INCLUDE_DIRS})
link_directories(${Python_LIBRARY_DIRS})
link_libraries(${Python_LIBRARIES})

# Basic functions
include_directories(common)
add_subdirectory(common)
include_directories(utils)
add_subdirectory(utils)

# Vector, matrix and basic linear algebra
include_directories(stream)
add_subdirectory(stream)
include_directories(matrix)
add_subdirectory(matrix)

# Speech features
include_directories(feature)
add_subdirectory(feature)

# Basic LTI processing
include_directories(convolution)
add_subdirectory(convolution)

# Subband processing
include_directories(modulated)
add_subdirectory(modulated)

# Adaptive processing
include_directories(aec)
add_subdirectory(aec)
include_directories(dereverberation)
add_subdirectory(dereverberation)
include_directories(square_root)
add_subdirectory(square_root)
include_directories(lms)
add_subdirectory(lms)
include_directories(beamformer)
add_subdirectory(beamformer)
include_directories(postfilter)
add_subdirectory(postfilter)

# time delay estimation
include_directories(tde)
add_subdirectory(tde)

# source localization
include_directories(localization)
add_subdirectory(localization)

include_directories(sad)
add_subdirectory(sad)

include_directories(objective_measure)
add_subdirectory(objective_measure)

# python libraries
include_directories(lib)
add_subdirectory(lib)