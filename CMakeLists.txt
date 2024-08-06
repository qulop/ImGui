# REQUIRES FOR THIS SCRIPT:
#   1) Define in root CMakeLists which backend you will use:
#       + IMGUI_OPENGL3_GLFW
#       + IMGUI_DX12_WIN32
#       + IMGUI_VULKAN_GLFW
#      For example: set(IMGUI_OPENGL3_GLFW ON CACHE BOOL "" FORCE)
#   1.2) If you select OpenGL3/GLFW backend, you should in advance define IMGUI_GLFW_PATH variable, which 
#        contain path to GLFW from your root project.

cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
project("ImGui" LANGUAGES CXX C)

option(IMGUI_OPENGL3_GLFW "Build OpenGL3/GLFW Backend" OFF)
option(IMGUI_DX12_WIN32 "Build DirectX12/Win32 Backend" OFF)
option(IMGUI_VULKAN_GLFW "Build Vulkan/GLFW Backend" OFF)

set(IMGUI_CORE_SRC     ${CMAKE_CURRENT_SOURCE_DIR}/imgui.cpp
                        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_demo.cpp
                        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_draw.cpp
                        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_tables.cpp
                        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_widgets.cpp)

set(IMGUI_BACKEND_SRC)
if(IMGUI_OPENGL3_GLFW)
    message(STATUS "ImGui backend selected: OpenGL3/GLFW")
    add_subdirectory(${IMGUI_GLFW_PATH} ${CMAKE_CURRENT_BINARY_DIR}/ImGui)

    list(APPEND IMGUI_BACKEND_SRC   ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.cpp
                                    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp)
elseif(IMGUI_DX12_WIN32)
    message(STATUS "ImGui backend selected: DirectX12/Win32")
    list(APPEND IMGUI_BACKEND_SRC   ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_win32.cpp
                                    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_dx12.cpp)
elseif(IMGUI_VULKAN_GLFW)
    message(STATUS "ImGui backend selected: Vulkan/GLFW")
    list(APPEND IMGUI_BACKEND_SRC   ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.cpp
                                    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_vulkan.cpp)
endif()


add_library(${PROJECT_NAME} STATIC ${IMGUI_CORE_SRC} ${IMGUI_BACKEND_SRC})
set_target_properties(${PROJECT_NAME} PROPERTIES LINKER_LANGUAGE CXX)

target_include_directories(${PROJECT_NAME}          PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}
                                                    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/backends)

if(IMGUI_OPENGL3_GLFW)
    find_package(OpenGL REQUIRED)

    target_include_directories(${PROJECT_NAME} PUBLIC ${IMGUI_GLFW_PATH}/include)
    target_link_libraries(${PROJECT_NAME} PRIVATE OpenGL::GL glfw)
endif()

