# cmake version, project name, language
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)
project(datetime-fortran Fortran)

# set output paths for modules, archives, and executables
set(CMAKE_Fortran_MODULE_DIRECTORY ${PROJECT_BINARY_DIR}/include)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# if build type not specified, default to release
# set(CMAKE_BUILD_TYPE "debug")
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "release")
endif()

# compiler flags for gfortran
if(CMAKE_Fortran_COMPILER_ID MATCHES GNU)
  set(CMAKE_Fortran_FLAGS_DEBUG "-O0 -g -C -fbacktrace")
  set(CMAKE_Fortran_FLAGS_RELEASE "-O3")
endif()

# compiler flags for ifort
if(CMAKE_Fortran_COMPILER_ID MATCHES Intel)
  set(CMAKE_Fortran_FLAGS "${CMAKE_Fortran_FLAGS} -assume realloc_lhs -heap-arrays -traceback -fp-model precise -fp-model source -align all")
  set(CMAKE_Fortran_FLAGS_DEBUG "-O0 -g -C")
  set(CMAKE_Fortran_FLAGS_RELEASE "-g3 -O1 -axCORE-AVX2 -debug all -check none -qopt-report=5 -qopt-report-annotate")
endif()

# library to archive (libdatetime.a)
add_library(datetime src/lib/datetime.f90 src/lib/mod_datetime.f90 src/lib/mod_timedelta.f90 src/lib/mod_clock.f90 src/lib/mod_strftime.f90 src/lib/mod_constants.f90)

# tests
enable_testing()
add_executable(datetime_tests src/tests/datetime_tests.f90)
target_link_libraries(datetime_tests datetime)
add_test(datetime_tests bin/datetime_tests)
