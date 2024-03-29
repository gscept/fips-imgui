#
# project: fips-imgui
#
    cmake_minimum_required(VERSION 3.21)

if (NOT FIPS_IMPORT)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
    project(fips-imgui)
    fips_setup()
endif()

fips_begin_lib(imgui)
    if(FIPS_WINDOWS)
        set_target_properties(imgui PROPERTIES COMPILE_FLAGS /wd4312)
    endif()
    target_include_directories(imgui PUBLIC imgui)
    fips_dir(imgui)
    fips_files(
        imconfig.h
        imgui.cpp
        imgui.h
        imgui_demo.cpp
        imgui_draw.cpp
        imgui_tables.cpp
		imgui_widgets.cpp
        imgui_internal.h
        imstb_rectpack.h
        imstb_textedit.h
        imstb_truetype.h
    )
fips_end_lib()
target_compile_definitions(imgui PUBLIC IMGUI_DISABLE_OBSOLETE_FUNCTIONS)

if (NOT FIPS_IMPORT)
    if (FIPS_MACOS OR FIPS_LINUX OR FIPS_WINDOWS)
        fips_begin_app(opengl2_example cmdline)
            include_directories(imgui/examples/opengl2_example)
            fips_dir(imgui/examples/opengl2_example)
            fips_files(main.cpp imgui_impl_glfw.h imgui_impl_glfw.cpp)
            fips_deps(imgui glfw3)

            if (FIPS_WINDOWS)
                fips_libs(opengl32)
            endif()

            if (FIPS_LINUX)
                fips_libs(X11 Xrandr Xi Xinerama Xxf86vm Xcursor GL m)
            endif()

            if (FIPS_MACOS)
                fips_frameworks_osx(Cocoa CoreVideo OpenGL)
                fips_libs(m)
            endif()
        fips_end_app()

        fips_begin_app(opengl3_example cmdline)
            include_directories(imgui/examples/libs/gl3w)
            include_directories(imgui/examples/opengl3_example)
            fips_dir(imgui/examples/libs/gl3w/GL)
            fips_files(gl3w.c gl3w.h glcorearb.h)
            fips_dir(imgui/examples/opengl3_example)
            fips_files(main.cpp imgui_impl_glfw_gl3.h imgui_impl_glfw_gl3.cpp)
            fips_deps(imgui glfw3)

            if (FIPS_WINDOWS)
                fips_libs(opengl32)
            endif()

            if (FIPS_LINUX)
                fips_libs(X11 Xrandr Xi Xinerama Xxf86vm Xcursor GL m dl)
            endif()

            if (FIPS_MACOS)
                fips_frameworks_osx(Cocoa CoreVideo OpenGL)
                fips_libs(m dl)
            endif()
        fips_end_app()
    endif()

    if (FIPS_WINDOWS)
        fips_begin_app(directx9_example cmdline)
            include_directories(imgui/examples/directx9_example "$ENV{DXSDK_DIR}Include")
            fips_dir(imgui/examples/directx9_example)
            fips_files(main.cpp imgui_impl_dx9.h imgui_impl_dx9.cpp)
            fips_deps(imgui)
            fips_libs(d3d9 d3dx9d dxerr dxguid)
        fips_end_app()

        fips_begin_app(directx10_example cmdline)
            include_directories(imgui/examples/directx10_example "$ENV{DXSDK_DIR}Include")
            fips_dir(imgui/examples/directx10_example)
            fips_files(main.cpp imgui_impl_dx10.h imgui_impl_dx10.cpp)
            fips_deps(imgui)
            fips_libs(d3d10 d3dcompiler dxgi imm32)
        fips_end_app()

        fips_begin_app(directx11_example cmdline)
            add_definitions(-DUNICODE -D_UNICODE)
            include_directories(imgui/examples/directx11_example)
            fips_dir(imgui/examples/directx11_example)
            fips_files(main.cpp imgui_impl_dx11.h imgui_impl_dx11.cpp)
            fips_deps(imgui)
            fips_libs(d3d11 d3dcompiler dxgi imm32)
        fips_end_app()
    endif()
endif()

if (FIPS_CLANG)
    set_target_properties(imgui PROPERTIES COMPILE_FLAGS "-Wno-type-limits -Wno-missing-field-initializers")
elseif(FIPS_GCC)
    set_target_properties(imgui PROPERTIES COMPILE_FLAGS "-Wno-unused-but-set-variable -Wno-type-limits -Wno-missing-field-initializers")
endif()