cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
project("ImGui" LANGUAGES CXX C)

option(IMGUI_OPENGL3_GLFW "" ON)

# Maybe in future
# option(IMGUI_DX12_WIN32 "" OFF)
# option(IMGUI_VULKAN_GLFW "" ON)

set(IMGUI_CORE_SRC     ${CMAKE_CURRENT_SOURCE_DIR}/imgui.cpp
                        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_demo.cpp
                        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_draw.cpp
                        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_tables.cpp
                        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_widgets.cpp)

set(IMGUI_BACKEND_SRC)

if (IMGUI_OPENGL3_GLFW)
    list(APPEND IMGUI_BACKEND_SRC   ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.cpp
                                    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp)
endif()


add_library(${CMAKE_PROJECT_NAME} STATIC ${IMGUI_CORE_SRC} ${IMGUI_BACKEND_SRC})
set_target_properties(${CMAKE_PROJECT_NAME} PROPERTIES LINKER_LANGUAGE CXX)

target_include_directories(${CMAKE_PROJECT_NAME}    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}
                                                    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/backends
                                                    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/third_party/glfw/include)

find_package(OpenGL REQUIRED)
target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE OpenGL::GL glfw ${GLFW_LIBRARIES})
