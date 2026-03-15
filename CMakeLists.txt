project(wgpu_native)

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(WGPU_NATIVE_LIB_TYPE debug)
    set(WGPU_NATIVE_TARGET lib-native)
else()
    set(WGPU_NATIVE_LIB_TYPE release)
    set(WGPU_NATIVE_TARGET lib-native-release)
endif()

add_custom_target(wgpu_native_lib
        COMMAND make ${WGPU_NATIVE_TARGET}
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        BYPRODUCTS ${CMAKE_CURRENT_SOURCE_DIR}/target/${WGPU_NATIVE_LIB_TYPE}/libwgpu_native.a
                   ${CMAKE_CURRENT_SOURCE_DIR}/target/${WGPU_NATIVE_LIB_TYPE}/wgpu_native.lib)

add_library(wgpu_native INTERFACE)

target_include_directories(wgpu_native INTERFACE
        ${CMAKE_CURRENT_SOURCE_DIR}/ffi/webgpu-headers
        ${CMAKE_CURRENT_SOURCE_DIR}/ffi)

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin" OR ${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    target_link_libraries(wgpu_native INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/target/${WGPU_NATIVE_LIB_TYPE}/libwgpu_native.a)
elseif (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    target_link_libraries(wgpu_native INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/target/${WGPU_NATIVE_LIB_TYPE}/wgpu_native.lib)
endif()

if (APPLE)
    target_link_libraries(wgpu_native INTERFACE
            "-framework Metal -framework QuartzCore"
            "-framework MetalKit -framework CoreFoundation"
            "-framework Foundation")
endif()