cmake_minimum_required(VERSION 3.10)

project(ffmpeg_vulkan_test)

add_executable(${CMAKE_PROJECT_NAME} testffmpeg.c testffmpeg_vulkan.c)

target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE 
        ${CMAKE_SOURCE_DIR}/include)

find_library(SDL3_LIB NAME SDL3 REQUIRED)
find_library(SDL3_TEST_LIB NAME SDL3_test REQUIRED)

# # Find Vulkan
# find_package(PkgConfig REQUIRED)
# pkg_check_modules(VULKAN REQUIRED vulkan)

# Check for EGL support
find_package(PkgConfig QUIET)
if(PkgConfig_FOUND)
    pkg_check_modules(EGL egl)
    if(EGL_FOUND)
        target_compile_definitions(${CMAKE_PROJECT_NAME} PRIVATE HAVE_EGL=1)
        target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE ${EGL_INCLUDE_DIRS})
        target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE ${EGL_LIBRARIES})
    endif()
    
    # Also check for OpenGL ES
    pkg_check_modules(GLES2 glesv2)
    if(GLES2_FOUND)
        target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE ${GLES2_INCLUDE_DIRS})
        target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE ${GLES2_LIBRARIES})
    endif()
endif()

##
# Find FFmpeg Libraries.
#
# Once done this will define
#   FFMPEG_FOUND        - System has the all required components.
#   FFMPEG_INCLUDE_DIRS - Include directory necessary for using the required components headers.
#   FFMPEG_LIBRARIES    - Link these to use the required ffmpeg components.
##

##
# FFMPEG_FOUND set to TRUE as default.
# Will be set to FALSE in case one of the required components is not found.
##
set(${FFMPEG_FOUND} TRUE)

##
# Find libavcodec.
##
find_path(AVCODEC_INCLUDE_DIR libavcodec/avcodec.h)
find_library(AVCODEC_LIBRARY avcodec)

##
# Add in libavcodec if found.
##
if (AVCODEC_INCLUDE_DIR AND AVCODEC_LIBRARY)
    list(APPEND FFMPEG_INCLUDE_DIRS ${AVCODEC_INCLUDE_DIR})
    list(APPEND FFMPEG_LIBRARIES ${AVCODEC_LIBRARY})
    message("AVCODEC found: ${AVCODEC_INCLUDE_DIR} ${AVCODEC_LIBRARY}")
else()
    set(${FFMPEG_FOUND} FALSE)
endif(AVCODEC_INCLUDE_DIR AND AVCODEC_LIBRARY)

##
# Find libavformat
##
find_path(AVFORMAT_INCLUDE_DIR libavformat/avformat.h)
find_library(AVFORMAT_LIBRARY avformat)

##
# Add in libavformat if found.
##
if (AVFORMAT_INCLUDE_DIR AND AVFORMAT_LIBRARY)
    list(APPEND FFMPEG_INCLUDE_DIRS ${AVFORMAT_INCLUDE_DIR})
    list(APPEND FFMPEG_LIBRARIES ${AVFORMAT_LIBRARY})
    message("AVFORMAT found: ${AVFORMAT_INCLUDE_DIR} ${AVFORMAT_LIBRARY}")
else()
    set(${FFMPEG_FOUND} FALSE)
endif(AVFORMAT_INCLUDE_DIR AND AVFORMAT_LIBRARY)

##
# Find libavdevice.
##
find_path(AVDEVICE_INCLUDE_DIR libavdevice/avdevice.h)
find_library(AVDEVICE_LIBRARY avdevice)

##
# Add in libavdevice if found.
##
if (AVDEVICE_INCLUDE_DIR AND AVDEVICE_LIBRARY)
    list(APPEND FFMPEG_INCLUDE_DIRS ${AVDEVICE_INCLUDE_DIR})
    list(APPEND FFMPEG_LIBRARIES ${AVDEVICE_LIBRARY})
    message("AVDEVICE found: ${AVDEVICE_INCLUDE_DIR} ${AVDEVICE_LIBRARY}")
else()
    set(${FFMPEG_FOUND} FALSE)
endif(AVDEVICE_INCLUDE_DIR AND AVDEVICE_LIBRARY)

##
# Find libavutil.
##
find_path(AVUTIL_INCLUDE_DIR libavutil/avutil.h)
find_library(AVUTIL_LIBRARY avutil)

##
# Add in libavutil if found.
##
if (AVUTIL_INCLUDE_DIR AND AVUTIL_LIBRARY)
    list(APPEND FFMPEG_INCLUDE_DIRS ${AVUTIL_INCLUDE_DIR})
    list(APPEND FFMPEG_LIBRARIES ${AVUTIL_LIBRARY})
    message("AVUTIL found: ${AVUTIL_INCLUDE_DIR} ${AVUTIL_LIBRARY}")
else()
    set(${FFMPEG_FOUND} FALSE)
endif(AVUTIL_INCLUDE_DIR AND AVUTIL_LIBRARY)

##
# Find libavfilter.
##
find_path(AVFILTER_INCLUDE_DIR libavfilter/avfilter.h)
find_library(AVFILTER_LIBRARY avfilter)

##
# Add in libavfilter if found.
##
if (AVFILTER_INCLUDE_DIR AND AVFILTER_LIBRARY)
    list(APPEND FFMPEG_INCLUDE_DIRS ${AVFILTER_INCLUDE_DIR})
    list(APPEND FFMPEG_LIBRARIES ${AVFILTER_LIBRARY})
    message("AVFILTER found: ${AVFILTER_INCLUDE_DIR} ${AVFILTER_LIBRARY}")
else()
    set(${FFMPEG_FOUND} FALSE)
endif(AVFILTER_INCLUDE_DIR AND AVFILTER_LIBRARY)

##
# Find libswscale.
##
find_path(SWSCALE_INCLUDE_DIR libswscale/swscale.h)
find_library(SWSCALE_LIBRARY swscale)

##
# Add in libswscale if found.
##
if (SWSCALE_INCLUDE_DIR AND SWSCALE_LIBRARY)
    list(APPEND FFMPEG_INCLUDE_DIRS ${SWSCALE_INCLUDE_DIR})
    list(APPEND FFMPEG_LIBRARIES ${SWSCALE_LIBRARY})
    message("SWSCALE found: ${SWSCALE_INCLUDE_DIR} ${SWSCALE_LIBRARY}")
else()
    set(${FFMPEG_FOUND} FALSE)
endif(SWSCALE_INCLUDE_DIR AND SWSCALE_LIBRARY)

##
# Find libswresample.
##
find_path(SWRESAMPLE_INCLUDE_DIR libswresample/swresample.h)
find_library(SWRESAMPLE_LIBRARY swresample)

##
# Add in libswresample if found.
##
if (SWRESAMPLE_INCLUDE_DIR AND SWRESAMPLE_LIBRARY)
    list(APPEND FFMPEG_INCLUDE_DIRS ${SWRESAMPLE_INCLUDE_DIR})
    list(APPEND FFMPEG_LIBRARIES ${SWRESAMPLE_LIBRARY})
    message("SWRESAMPLE found: ${SWRESAMPLE_INCLUDE_DIR} ${SWRESAMPLE_LIBRARY}")
else()
    set(${FFMPEG_FOUND} FALSE)
endif(SWRESAMPLE_INCLUDE_DIR AND SWRESAMPLE_LIBRARY)


target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE ${SDL3_LIB})
target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE ${SDL3_TEST_LIB})
target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE ${FFMPEG_LIBRARIES})

# Add math library (for some systems)
target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE m)

# # Add Vulkan support
# if(VULKAN_FOUND)
#     target_compile_definitions(${CMAKE_PROJECT_NAME} PRIVATE FFMPEG_VULKAN_SUPPORT=1)
#     target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE ${VULKAN_INCLUDE_DIRS})
#     target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE ${VULKAN_LIBRARIES})
#     message("Vulkan found: ${VULKAN_INCLUDE_DIRS} ${VULKAN_LIBRARIES}")
# else()
#     message(WARNING "Vulkan not found, Vulkan video acceleration will not be available")
# endif()