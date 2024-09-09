cmake_minimum_required(VERSION 3.28)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON) # needed for Clang-Tidy

project(fast_snail_server CXX)

include(cmake/setup.cmake)

add_library(eevee_options INTERFACE)

include(cmake/CompilerWarnings.cmake)
myproject_set_project_warnings(eevee_options ON "" "" "" "")

if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
include(cmake/StaticAnalyzers.cmake)
myproject_enable_clang_tidy(eevee_options ON)

  include(cmake/Sanitizers.cmake)
myproject_enable_sanitizers(eevee_options ON ON ON OFF OFF)
endif()
# responsible for connecting packages
find_package(Boost 1.84.0 REQUIRED COMPONENTS log log_setup locale filesystem atomic locale chrono thread context)
find_package(OpenCLHeaders REQUIRED)
find_package(fmt REQUIRED)
find_package(OpenSSL REQUIRED)

add_library(eevee_deps INTERFACE )
# responsible for connecting interfaces

target_link_libraries(eevee_deps INTERFACE Boost::headers Boost::log Boost::log_setup Boost::locale Boost::filesystem Boost::atomic Boost::chrono Boost::thread Boost::context OpenCL::Headers OpenSSL::SSL OpenSSL::Crypto)
if(MSVC)
  target_compile_definitions(eevee_deps INTERFACE _WIN32_WINNT=0x0A00) # Windows 10
endif()
target_include_directories(eevee_deps INTERFACE ${Boost_INCLUDE_DIRS})

add_subdirectory(src)