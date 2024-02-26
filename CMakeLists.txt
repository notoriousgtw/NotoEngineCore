cmake_minimum_required(VERSION 3.16)
project(NotoEngine)


set(CMAKE_CXX_STANDARD 20)

add_executable(NotoEngine src/main.cpp
)


find_package(Vulkan REQUIRED)
target_include_directories(${PROJECT_NAME} PUBLIC ${Vulkan_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} PRIVATE Vulkan::Vulkan)

#find_package(VulkanMemoryAllocator CONFIG REQUIRED)
#target_link_libraries(${PROJECT_NAME} PRIVATE GPUOpen::VulkanMemoryAllocator)

find_package(glfw3 CONFIG REQUIRED)
target_link_libraries(${PROJECT_NAME} PRIVATE glfw)

find_package(glm CONFIG REQUIRED)
target_link_libraries(${PROJECT_NAME} PRIVATE glm::glm)

find_package(fmt CONFIG REQUIRED)
target_link_libraries(${PROJECT_NAME} PRIVATE fmt::fmt)

find_path(STB_INCLUDE_DIRS "stb.h")
target_include_directories(${PROJECT_NAME} PRIVATE ${STB_INCLUDE_DIRS})

find_package(imgui CONFIG REQUIRED)
target_link_libraries(${PROJECT_NAME} PRIVATE imgui::imgui)

find_package(fastgltf CONFIG REQUIRED)
target_link_libraries(${PROJECT_NAME} PRIVATE fastgltf::fastgltf)

find_program(GLSLC glslc)
if (NOT GLSLC)
    message(FATAL_ERROR "Generator not found!")
endif ()

# Automatically download cmake-spirv with FetchContent
include(FetchContent)

FetchContent_Declare(cmake-spirv
        GIT_REPOSITORY https://github.com/liliolett/cmake-spirv.git
        GIT_TAG        origin/v1
)
FetchContent_MakeAvailable(cmake-spirv)

# Add cmake-spirv to module path
list(APPEND CMAKE_MODULE_PATH ${cmake-spirv_SOURCE_DIR}/include)

# Include required modules
include(AddSpirvModules)

# Create shader target
add_spirv_modules(shaders
        SOURCE_DIR shaders
        BINARY_DIR shaders
        SOURCES    simple.vert simple.frag)

# Make sure shaders are compiled by specifying dependencies
add_dependencies(${PROJECT_NAME} shaders)