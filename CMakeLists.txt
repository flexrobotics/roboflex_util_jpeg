cmake_minimum_required(VERSION 3.18)

project(roboflex_util_jpeg VERSION 0.1.3 DESCRIPTION "roboflex util jpeg")

# specify the C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)


# -------------------- 
# Resolve dependencies

include(FetchContent)

# Fetch and make jpeg-compressor available
FetchContent_Declare(
  jpeg-compressor
  URL https://github.com/richgel999/jpeg-compressor/archive/refs/tags/v104.tar.gz
)

FetchContent_MakeAvailable(jpeg-compressor)

# Specify the source directory
set(JPEG_COMPRESSOR_SRC_DIR "${jpeg-compressor_SOURCE_DIR}")

message("JPEG_COMPRESSOR_SRC_DIR: ${JPEG_COMPRESSOR_SRC_DIR}")

# Create the jpeg-compressor library
add_library(jpeg-compressor STATIC
  ${JPEG_COMPRESSOR_SRC_DIR}/jpgd.cpp
  ${JPEG_COMPRESSOR_SRC_DIR}/jpge.cpp
)
set_property(TARGET jpeg-compressor PROPERTY 
    POSITION_INDEPENDENT_CODE ON
)

# Specify the include directory for the jpeg-compressor library
target_include_directories(jpeg-compressor PUBLIC 
    ${JPEG_COMPRESSOR_SRC_DIR}
)


# download and build roboflex_core
FetchContent_Declare(roboflex_core
    GIT_REPOSITORY https://github.com/flexrobotics/roboflex.git
    GIT_TAG        main
)
set(BUILD_ROBOFLEX_PYTHON_EXT OFF CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(roboflex_core)


# -------------------- 
# Define the library

add_library(roboflex_util_jpeg STATIC
    src/jpeg.cpp
    include/roboflex_util_jpeg/jpeg.h
)

# Set some properties on our library
set_property(TARGET roboflex_util_jpeg PROPERTY 
    POSITION_INDEPENDENT_CODE ON
)

# Include directories when we compile our library
target_include_directories(roboflex_util_jpeg PUBLIC 
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include> 
    $<INSTALL_INTERFACE:include>
    ${JPEG_COMPRESSOR_SRC_DIR}
    #${xtensor_INCLUDE_DIRS}
)

# Link against the necessary libraries
target_link_libraries(roboflex_util_jpeg PUBLIC 
    roboflex_core 
    jpeg-compressor
)


# -------------------- 
# install

# If you need to install the util_jpeg library
install(TARGETS roboflex_util_jpeg 
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    INCLUDES DESTINATION include
)
#install(FILES jpeg.h DESTINATION include/roboflex_util_jpeg)
install(DIRECTORY include/roboflex_util_jpeg
    DESTINATION include
)


# --------------------
# build python bindings

add_subdirectory(python)
