cmake_minimum_required(VERSION 2.8)
project(graphics)

if(CMAKE_SIZEOF_VOID_P EQUAL 8) # 64 bit architecture detected
    message("Detected 64bit build arch. Setting 32bit build options.")
    message("This requires a multilib cross-compiler. Let's hope you have one.")
    SET_PROPERTY(GLOBAL PROPERTY FIND_LIBRARY_USE_LIB64_PATHS 0)
    add_definitions(-m32)
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -m32")
endif()

set(CMAKE_BUILD_TYPE Debug)

add_definitions(-Dunix -Dlinux -std=c++11 -D_GLIBCXX_USE_CXX11_ABI=0)

find_package(OpenGL REQUIRED)
find_package(SDL REQUIRED)
find_package(SDL_image REQUIRED)
find_package(SDL_ttf REQUIRED)
find_package(GTK2 REQUIRED gtk)
find_package(OpenGL REQUIRED)
find_package(GLEW REQUIRED)
find_package(OpenAL REQUIRED)
find_package(Freetype REQUIRED)
find_package(Threads REQUIRED)
find_package(X11 REQUIRED)

set(CURSES_NEED_NCURSES TRUE)
find_package(Curses REQUIRED)

# special case for X11 libs that the FindX11 module doesn't require by default
if(NOT X11_ICE_FOUND)
   message(FATAL_ERROR "\nThe X11 Inter-Client Exchange (ICE) Library could not be found.\nCannot continue. Please install libICE.\n")
endif()

if(NOT X11_SM_FOUND)
  message(FATAL_ERROR "\nThe X11 Session Management (SM) Library could not be found.\nCannot continue. Please install libSM.\n")
endif()

if(NOT X11_Xext_FOUND)
    message(FATAL_ERROR "\nThe X.Org X11 libXext runtime library could not be found.\nCannot continue. Please install libXext.\n")
endif()

SET(SOURCES
    g_src/basics.cpp g_src/command_line.cpp g_src/enabler.cpp g_src/enabler_input.cpp
	g_src/files.cpp g_src/find_files_posix.cpp g_src/graphics.cpp g_src/init.cpp
	g_src/interface.cpp g_src/keybindings.cpp g_src/KeybindingScreen.cpp
	g_src/random.cpp g_src/renderer_offscreen.cpp g_src/resize++.cpp
	g_src/textures.cpp g_src/textlines.cpp g_src/ttf_manager.cpp g_src/ViewBase.cpp
	g_src/win32_compat.cpp g_src/music_and_sound_openal.cpp
)

# Fix a weird case where despite our global property set at the top, it still picks up lib64 paths.
string(REPLACE "lib64" "lib32" GTK2_INCLUDE_DIRS "${GTK2_INCLUDE_DIRS}")

include_directories(
    ${OPENGL_INCLUDE_DIR}
    ${SDL_INCLUDE_DIR}
    ${SDL_TTF_INCLUDE_DIRS}
    ${SDL_IMAGE_INCLUDE_DIRS}
    ${OPENAL_INCLUDE_DIR}
    ${GTK2_INCLUDE_DIRS}
    ${GLEW_INCLUDE_DIRS}
    ${FREETYPE_INCLUDE_DIRS}
    ${X11_INCLUDE_DIR}
    ${CURSES_INCLUDE_DIR}
)

add_library(graphics SHARED ${SOURCES})
target_link_libraries(graphics
    ${OPENGL_LIBRARY}
    ${SDL_LIBRARY}
    ${SDL_TTF_LIBRARIES}
    ${SDL_IMAGE_LIBRARIES}
    ${OPENAL_LIBRARY}
    ${GTK2_LIBRARIES}
    ${GLEW_LIBRARIES}
    ${FREETYPE_LIBRARIES}
    ${X11_LIBRARIES}
    ${CURSES_LIBRARIES}
)
