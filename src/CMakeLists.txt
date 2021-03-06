#------------------------------------------------------------------------------
#  Copyright (c) 2018-2020 Michele Morrone
#  All rights reserved.
#
#  https://michelemorrone.eu - https://BrutPitt.com
#
#  twitter: https://twitter.com/BrutPitt - github: https://github.com/BrutPitt
#
#  mailto:brutpitt@gmail.com - mailto:me@michelemorrone.eu
#  
#  This software is distributed under the terms of the BSD 2-Clause license
#------------------------------------------------------------------------------
cmake_minimum_required(VERSION 3.15)
SET(BUILD_wglChAoSP TRUE CACHE BOOL TRUE)
SET(BUILD_wglChAoSP_lowres FALSE CACHE BOOL FALSE)
SET(BUILD_prng_test FALSE CACHE BOOL FALSE)
###########################################################
#
# *** CHANGES NOTE ***
# Now ALL EMITTER TYPES are selectable inside program
# Remain only differentiation on OpenGL type
# for EMSCRIPTEN is FIXED: Single thread & Aux Buffer
#
#   OpenGL 4.5 - Zero Driver Overhead
#
# OGL 4.5 Aux Thread and mappedBuffer on GPU (less GPU memory)
# set(OGL45_THREAD_MAPMEM "-DGLAPP_REQUIRE_OGL45 -DUSE_THREAD_TO_FILL -DUSE_MAPPED_BUFFER")
# OGL 4.5 Aux Thread and Aux buffer (fastest)
# set(OGL45_THREAD "-DGLAPP_REQUIRE_OGL45 -DUSE_THREAD_TO_FILL")
# OGL 4.5 Single thread, Aux buffer
# set(OGL45_BUFFER "-DGLAPP_REQUIRE_OGL45")
set(OGL45 "-DGLAPP_REQUIRE_OGL45")
#
#   OpenGL 4.1 - Olden GPU and MacOS X
#
# OGL 4.1 Aux Thread and Aux buffer  (fastest on openGL 4.1)
# set(OGL41_THREAD "-DUSE_THREAD_TO_FILL")
# OGL 4.1 Single thread, Aux buffer
# set(OGL41_BUFFER "")
set(OGL41 "")

#set(GLAPP_DISABLE_DLA "-DGLAPP_DISABLE_DLA")
set(GLAPP_DISABLE_DLA "")

set(SRC "${CMAKE_SOURCE_DIR}/src")

    set(SOURCE_FILES
        ${SRC}/libs/IconsFontAwesome/IconsFontAwesome.h
        ${SRC}/libs/imgui/imconfig.h
        ${SRC}/libs/imgui/imgui.cpp
        ${SRC}/libs/imgui/imgui_widgets.cpp
        ${SRC}/libs/imgui/imgui.h
        ${SRC}/libs/imgui/imgui_draw.cpp
        ${SRC}/libs/imgui/imgui_impl_glfw.cpp
        ${SRC}/libs/imgui/imgui_impl_glfw.h
        ${SRC}/libs/imgui/imgui_impl_opengl3.cpp
        ${SRC}/libs/imgui/imgui_impl_opengl3.h
        ${SRC}/libs/imgui/imgui_internal.h
        ${SRC}/libs/imgui/imstb_rectpack.h
        ${SRC}/libs/imgui/imstb_textedit.h
        ${SRC}/libs/imgui/imstb_truetype.h
        ${SRC}/libs/configuru/configuru.hpp
        ${SRC}/tools/fastPRNG.cpp
        ${SRC}/tools/fastPRNG.h
        ${SRC}/tools/imGuIZMOquat.cpp
        ${SRC}/tools/imGuIZMOquat.h
        ${SRC}/tools/imguiControls.cpp
        ${SRC}/tools/imguiControls.h
        ${SRC}/tools/glslProgramObject.cpp
        ${SRC}/tools/glslProgramObject.h
        ${SRC}/tools/glslShaderObject.cpp
        ${SRC}/tools/glslShaderObject.h
        ${SRC}/tools/transforms.h
        ${SRC}/tools/vgMath.h
        ${SRC}/tools/vgConfig.h
        ${SRC}/tools/vGizmo.h
        ${SRC}/ui/uiAttractorsDlg.cpp
        ${SRC}/ui/uiMainDlg.cpp
        ${SRC}/ui/uiMainDlg.h
        ${SRC}/ui/uiSettings.cpp
        ${SRC}/appDefines.h
        ${SRC}/attractorsBase.cpp
        ${SRC}/attractorsBase.h
        ${SRC}/attractorsDLA3D.cpp
        ${SRC}/attractorsDLA3D.h
        ${SRC}/attractorsFractalsIIM.cpp
        ${SRC}/attractorsFractalsIIM.h
        ${SRC}/attractorsDiffEq.cpp
        ${SRC}/attractorsDiffEq.h
        ${SRC}/attractorsMagnetic.cpp
        ${SRC}/attractorsMagnetic.h
        ${SRC}/attractorsPolynomial.cpp
        ${SRC}/attractorsPolynomial.h
        ${SRC}/attractorsTrigonom.cpp
        ${SRC}/attractorsTrigonom.h
        ${SRC}/attractors2DTransf.cpp
        ${SRC}/attractors2DTransf.h
        ${SRC}/attractorsRampe.cpp
        ${SRC}/attractorsRampe.h
        ${SRC}/attractorsFiles.cpp
        ${SRC}/attractorsStartVals.cpp
        ${SRC}/configFile.cpp
        ${SRC}/glApp.cpp
        ${SRC}/glApp.h
        ${SRC}/glWindow.cpp
        ${SRC}/glWindow.h
        ${SRC}/IFS.cpp
        ${SRC}/IFS.h
        ${SRC}/partSystem.cpp
        ${SRC}/partSystem.h
        ${SRC}/emitter.cpp
        ${SRC}/emitter.h
        ${SRC}/mmFBO.cpp
        ${SRC}/mmFBO.h
        ${SRC}/palettes.cpp
        ${SRC}/palettes.h
        ${SRC}/ParticlesUtils.cpp
        ${SRC}/ParticlesUtils.h
        ${SRC}/ShadersClasses.cpp
        ${SRC}/ShadersClasses.h
        ${SRC}/vertexbuffer.h)

###########################################################
#   EMSCRIPTEN setting
###########################################################
if (BUILD_wglChAoSP)
    if(BUILD_wglChAoSP_lowres) 
        project(wglChAoSP_lowres)
        set(LIGHTVER_EXPERIMENTAL "")
    else()
        set(LIGHTVER_EXPERIMENTAL "-DGLCHAOSP_LIGHTVER_EXPERIMENTAL")
        project(wglChAoSP)
    endif()

    set(OGL_EMITTER_TYPE ${OGL41})
    
    #set(CMAKE_CXX_STANDARD 14)
    find_package(OpenGL)

    #add EMSCRIPTEN PATH with preference to command-line define
    if(EXISTS ${EMSCRIPTEN_ROOT_PATH})
        include_directories(BEFORE SYSTEM ${EMSCRIPTEN_ROOT_PATH}/system/include)
    else()
        if(EXISTS $ENV{EMSCRIPTEN})
            include_directories(BEFORE SYSTEM $ENV{EMSCRIPTEN}/system/include)
        endif()
    endif()
    include_directories(${SRC})
    include_directories(${SRC}/tools)
    include_directories(${SRC}/libs)
    include_directories(${SRC}/libs)
    #add also environment include
    #include_directories(AFTER $ENV{INCLUDE})

# -o ${PROJECT_NAME}.html ->  CMAKE_EXECUTABLE_SUFFIX  .html .bc, .js (default), etc
set(CMAKE_EXECUTABLE_SUFFIX ".html")

# emcc compiler options
    add_definitions(${LIGHTVER_EXPERIMENTAL} -DGLAPP_NO_GLSL_PIPELINE -DVGM_USES_TEMPLATE -DIMGUI_IMPL_OPENGL_ES3 -DGLCHAOSP_LIGHTVER -DGLCHAOSP_USE_LOWPRECISION -DGLFW_INCLUDE_NONE -DGLM_ENABLE_EXPERIMENTAL)
    add_definitions(-DGLAPP_NO_OGL_DSA -DGLFW_INCLUDE_ES3)
    add_compile_options(-Wno-parentheses -Wno-format-security -Wno-logical-op-parentheses -Wno-unused-value -Wdelete-non-virtual-dtor -fpermissive)

# BUILD TYPE: [Debug|RelWithDebInfo|Release|MinSizeRel]    
    if(${CMAKE_BUILD_TYPE} MATCHES "Debug")
        set(M_EMCC_BINARYEN "-s WASM=0 -s DEMANGLE_SUPPORT=1 -s LZ4=1")
        set(M_EMCC_SHELL_FILE "--shell-file \"${CMAKE_SOURCE_DIR}/shell_minimal.html\"")
    else()
        #  -s BINARYEN_METHOD='native-wasm,asmjs' 'interpret-asm2wasm' -s BINARYEN_ASYNC_COMPILATION=0
        if(${CMAKE_BUILD_TYPE} MATCHES "RelWithDebInfo")
            set(M_EMCC_BINARYEN "-s WASM=1")
            set(M_EMCC_SHELL_FILE "--shell-file \"${CMAKE_SOURCE_DIR}/shell_minimal.html\"")
        else()
            if(${CMAKE_BUILD_TYPE} MATCHES "MinSizeRel")
                # one file no memory option: -s SINGLE_FILE=1 --memory-init-file 0
                # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3")
                set(M_EMCC_BINARYEN "--no-heap-copy --closure 1 -s WASM=1 -s LZ4=1")
                set(M_EMCC_SHELL_FILE "--shell-file \"${CMAKE_SOURCE_DIR}/mVeryMinimal.html\"")
            else() # Release 
                # -s SINGLE_FILE=1 --memory-init-file 0 -s \"BINARYEN_METHOD='interpret-asm2wasm'\"
                set(M_EMCC_BINARYEN "-s WASM=1 ")
                set(M_EMCC_SHELL_FILE "--shell-file \"${CMAKE_SOURCE_DIR}/mVeryMinimal.html\"")
            endif()    
        endif()    
    endif()

    set(M_EMCC_EMBED_ALL_ATTRACTORS "${CMAKE_SOURCE_DIR}/../ChaoticAttractors@ChaoticAttractors/")
    set(M_EMCC_EMBED_FEW_ATTRACTORS "${CMAKE_SOURCE_DIR}/../selectedAttractors@ChaoticAttractors/")

# grouped EMCC_OPTIONS
#     --embed-file \"${CMAKE_SOURCE_DIR}/../ChaoticAttractors@ChaoticAttractors/\"\

    if(BUILD_prng_test)
        set(M_EMCC_EMBED_FILES "--preload-file \"${CMAKE_SOURCE_DIR}/../Shaders@Shaders/\"\
         --preload-file \"${CMAKE_SOURCE_DIR}/../colorMaps/palettes.json@colorMaps/\"\
         --preload-file \"${CMAKE_SOURCE_DIR}/../Fonts@Fonts/\"\
         --preload-file \"${CMAKE_SOURCE_DIR}/../rndData@rndData/\"")
        add_definitions(-DGLCHAOSP_TEST_RANDOM_DISTRIBUTION)
    else()
        set(M_EMCC_EMBED_FILES "--preload-file \"${CMAKE_SOURCE_DIR}/../Shaders@Shaders/\"\
         --preload-file \"${M_EMCC_EMBED_FEW_ATTRACTORS}\"\
         --preload-file \"${CMAKE_SOURCE_DIR}/../colorMaps/palettes.json@colorMaps/\"\
         --preload-file \"${CMAKE_SOURCE_DIR}/../Fonts@Fonts/\"\
         --preload-file \"${CMAKE_SOURCE_DIR}/../startData@startData/\"")
    endif()
     
#     --embed-file \"${CMAKE_SOURCE_DIR}/../Fonts/Cousine-Regular.ttf@Fonts/\"\
#     --embed-file \"${CMAKE_SOURCE_DIR}/../Fonts/fontawesome-webfont.ttf@Fonts/\"\

    # set(M_EMCC_EMBED_FILES "--preload-file \"${CMAKE_SOURCE_DIR}/Shaders@/Shaders\"")
    # -s FULL_ES3=1 -s OFFSCREENCANVAS_SUPPORT=1
    set(M_EMCC_OGL_OPTIONS "-s USE_WEBGL2=1 -s USE_GLFW=3 -s LEGACY_GL_EMULATION=0 -s OFFSCREENCANVAS_SUPPORT=1 ")
    set(M_EMCC_OPTIONS "-s DISABLE_DEPRECATED_FIND_EVENT_TARGET_BEHAVIOR=1 -s ALLOW_MEMORY_GROWTH=1 ${M_EMCC_SHELL_FILE} ${M_EMCC_OGL_OPTIONS} ${M_EMCC_BINARYEN} ${M_EMCC_EMBED_FILES}")

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OGL_EMITTER_TYPE} ${M_EMCC_OPTIONS} ${GLAPP_DISABLE_DLA}")

    add_executable(${PROJECT_NAME} 
                    ${SRC}/emsTouch.cpp
                    ${SRC}/emsTouch.h
                    ${SOURCE_FILES})

# relative emscripten output files (EMSOUT_PATH for my RAMDISK, if defined)
    if(EXISTS $ENV{EMSOUT_PATH})
        set(EMSOUT "$ENV{EMSOUT_PATH}")
    else()
        set(EMSOUT "${CMAKE_SOURCE_DIR}")
    endif()
    target_compile_features(${PROJECT_NAME}  PRIVATE cxx_std_14)

    if(EXISTS $ENV{RAMDISK}) #my RAMDISK env
        set_target_properties(${PROJECT_NAME} PROPERTIES RUNTIME_OUTPUT_DIRECTORY $ENV{RAMDISK}/emsOut/${CMAKE_BUILD_TYPE})
    else ()
        set_target_properties(${PROJECT_NAME} PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${EMSOUT}/emsOut/${CMAKE_BUILD_TYPE})
    endif()


###########################################################
#   PC setting
###########################################################
else()
#   Default best emitter type and OpenGL

    if(APPLE)
        project(glChAoSP_OSX)
    else()
        if(WIN32)
            project(glChAoSP)
        else()
            project(glChAoSP_Linux)
        endif()
    endif()

    #set(CMAKE_CXX_STANDARD 14)
    find_package(OpenGL)
    

if(OPENGL_FOUND)
    message(STATUS "OPENGL_INCLUDE_DIRS: ${OPENGL_INCLUDE_DIRS}")
    message(STATUS "OPENGL_LIBRARY: ${OPENGL_LIBRARY}")

    include_directories(${OPENGL_INCLUDE_DIRS})
else ()
    message (FATAL_ERROR "OPENGL not found... REQUIRED!!!!")
endif(OPENGL_FOUND)

add_executable(${PROJECT_NAME}
        ${SRC}/libs/glad/glad.cpp
        ${SRC}/libs/glad/glad.h
        ${SRC}/libs/lodePNG/lodepng.cpp
        ${SRC}/libs/lodePNG/lodepng.h
        ${SRC}/libs/tinyFileDialog/tinyfiledialogs.c
        ${SRC}/libs/tinyFileDialog/tinyfiledialogs.h
        ${SRC}/tools/oglAxes.cpp
        ${SRC}/tools/oglAxes.h
        ${SOURCE_FILES})

target_include_directories(${PROJECT_NAME} PUBLIC ${SRC})
target_include_directories(${PROJECT_NAME} PUBLIC ${SRC}/tools)
target_include_directories(${PROJECT_NAME} PUBLIC ${SRC}/libs)
target_include_directories(${PROJECT_NAME} PUBLIC ${SRC}/libs/glfw/include)
#target_include_directories(${PROJECT_NAME} PUBLIC $ENV{INCLUDE})

if(APPLE)
    target_link_directories(${PROJECT_NAME} PUBLIC ${SRC}/libs/glfw/buildOSX/lib)
else()
    if(WIN32)
        if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU") # MinGW
            # need to have in the LIB env var
        else()
            target_link_directories(${PROJECT_NAME} PRIVATE ${SRC}/libs/glfw/buildWin/lib/${CMAKE_BUILD_TYPE})
        endif()
    else()
    # both MinGW and Linux
        target_link_directories(${PROJECT_NAME} PRIVATE ${SRC}/libs/glfw/buildLinux/lib)
    endif(WIN32)
endif(APPLE)


add_definitions(-DVGM_USES_TEMPLATE -DGLFW_INCLUDE_NONE -DIMGUI_IMPL_OPENGL_LOADER_GLAD -DGLM_ENABLE_EXPERIMENTAL)

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    if(WIN32)
        set($ENV{CL},"")
        add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    endif()
    target_compile_options(${PROJECT_NAME} PRIVATE -Ofast -Wno-format-security -Wno-format-nonliteral -Wdeprecated-declarations -Wno-reorder -Wno-logical-op-parentheses -fexceptions -Wno-unused-command-line-argument -Wno-parentheses)
    message("Clang...")
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_compile_options(${PROJECT_NAME} PRIVATE -fopenmp -Wno-format-security -fpermissive)
    #add_compile_options(--verbose -Wno-format-security -std=c++14 -fpermissive)  -O3 -ftree-vectorize -mavx2
    message("GNU...")
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    # add_compile_options("/W1 /Qpar /Gm- /O2 /Ob2 /Zc:inline /fp:precise /WX- /Zc:forScope /Gd /FC /Fa /Ot /MP")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MP /W1 /Qpar /Gm- /O2 /Ob2 /Zc:inline /fp:precise /WX- /Zc:forScope /EHsc /Gd /FC /Fa /Ot")
    # Force to always compile with W1 (before CMake 3.15)
    # if(CMAKE_CXX_FLAGS MATCHES "/W[0-4]")
    #     string(REGEX REPLACE "/W[0-4]" "/W1" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
    # else()
    #     set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W1")
    # endif()
    # MSVC_RUNTIME_LIBRARY (from CMake 3.15)
    set_property(TARGET ${PROJECT_NAME} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /NODEFAULTLIB:MSVCRT.lib " )
    message("MSVC...")
endif()

#add_compile_options("${OGL_EMITTER_TYPE} ${COMMON_DEFINES} ${GLAPP_DISABLE_DLA}")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  ${OGL_EMITTER_TYPE} ${COMMON_DEFINES} ${GLAPP_DISABLE_DLA}")

if(EXISTS $ENV{RAMDISK}) #my RAMDISK env 
    set_target_properties(${PROJECT_NAME} PROPERTIES RUNTIME_OUTPUT_DIRECTORY $ENV{RAMDISK}/glChAoSP_release)
else()
    set_target_properties(${PROJECT_NAME} PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/..)
endif()

if(OPENGL_FOUND)
    if(APPLE)
        add_definitions(${OGL41} -DGLCHAOSP_NO_USES_GLSL_SUBS -DGLAPP_NO_GLSL_PIPELINE)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-logical-op-parentheses -Wno-unused-command-line-argument -Wno-parentheses -framework Cocoa -framework IOKit -framework CoreVideo")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-format-security -Wno-format-nonliteral -Wdeprecated-declarations -Wno-reorder -Wno-logical-op-parentheses -fexceptions -Wno-unused-command-line-argument -Wno-parentheses")
        set(TARGET_LIBS ${CMAKE_DL_LIBS} "-lglfw3 -lpthread")
        message("AppleClang...")
    else()
        if(NOT OGL_EMITTER_TYPE)
            set(OGL_EMITTER_TYPE ${OGL45})
        endif()
        add_definitions(${OGL_EMITTER_TYPE})

        if(WIN32)
            if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
                set(TARGET_LIBS ${CMAKE_DL_LIBS} "-lglfw3")
            else()
                set(TARGET_LIBS ${CMAKE_DL_LIBS} "../../src/libs/glfw/buildWin/lib/${CMAKE_BUILD_TYPE}/glfw3")
            endif()
        else()    
            set(TARGET_LIBS ${CMAKE_DL_LIBS} "-fopenmp -lglfw3 -lpthread -lX11")
        endif(WIN32)
    endif(APPLE)

    target_compile_features(${PROJECT_NAME}  PRIVATE cxx_std_14)
    target_link_libraries(${PROJECT_NAME} ${OPENGL_LIBRARY} ${TARGET_LIBS})
endif(OPENGL_FOUND)

endif() 
