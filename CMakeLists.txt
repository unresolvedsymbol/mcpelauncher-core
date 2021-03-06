cmake_minimum_required (VERSION 2.6)

project(mcpelauncher-core LANGUAGES CXX)

if (NOT APPLE)
    find_package(PNG)
endif()

add_library(mcpelauncher-core include/mcpelauncher/hook.h include/mcpelauncher/mod_loader.h include/mcpelauncher/hybris_utils.h include/mcpelauncher/patch_utils.h include/mcpelauncher/crash_handler.h include/mcpelauncher/minecraft_utils.h src/hook.cpp src/mod_loader.cpp src/hybris_utils.cpp src/hybris_android_log_hook.cpp src/crash_handler.cpp src/patch_utils.cpp src/minecraft_utils.cpp include/mcpelauncher/minecraft_version.h src/minecraft_version.cpp)
target_include_directories(mcpelauncher-core PUBLIC include/)
target_link_libraries(mcpelauncher-core PUBLIC mcpelauncher-common logger linker libc-shim minecraft-imported-symbols file-util ${CMAKE_DL_LIBS})

# Enter linker private compat
get_target_property(LINKER_PRIVATE_INCLUDE_DIRECTORIES linker INCLUDE_DIRECTORIES)
get_target_property(LINKER_PRIVATE_COMPILE_OPTIONS linker COMPILE_OPTIONS)
get_target_property(LINKER_PRIVATE_COMPILE_DEFINITIONS linker COMPILE_DEFINITIONS)
target_include_directories(mcpelauncher-core PRIVATE ${LINKER_PRIVATE_INCLUDE_DIRECTORIES})
target_compile_definitions(mcpelauncher-core PRIVATE ${LINKER_PRIVATE_COMPILE_DEFINITIONS})
target_compile_options(mcpelauncher-core PRIVATE ${LINKER_PRIVATE_COMPILE_OPTIONS})

if (APPLE)
    target_link_libraries(mcpelauncher-core osx-elf-header)
endif()

if (IS_ARMHF_BUILD AND NOT IS_64BIT)
    target_compile_definitions(mcpelauncher-core PRIVATE USE_BIONIC_LIBC)
endif()

if (PNG_FOUND)
    target_compile_definitions(mcpelauncher-core PRIVATE HAS_LIBPNG)
    target_include_directories(mcpelauncher-core PRIVATE ${PNG_INCLUDE_DIRS})
    target_link_libraries(mcpelauncher-core ${PNG_LIBRARIES})
endif()
