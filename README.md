## About Libretro core for ONScripter

ONScripter is a visual novel games engine compatible to NScripter,
this project provides a libretro core allowing the ONScripter visual
novel games to be played with libretro frontends such as RetroArch.

This version is based on the ogapee upstream, you may want to use
[OnscripterYuri](https://github.com/YuriSizuku/OnscripterYuri) for Chinese games.



This project is based on:
- bzip2: https://sourceware.org/bzip2/
- zlib: https://zlib.net/
- FreeType: https://www.freetype.org/
- libogg: https://xiph.org/ogg/
- libvorbis: https://xiph.org/vorbis/
- mad: https://www.underbit.com/products/mad/
- SDL, SDL_image, SDL_mixer, SDL_ttf: https://libsdl.org/
- ONScripter: https://github.com/ogapee/onscripter
- sdl-libretro: https://github.com/libretro/sdl-libretro
- libretro-common: https://github.com/libretro/libretro-common
- wuffs: https://github.com/google/wuffs

Thanks them!

## Building

### Linux

    git clone --recursive https://github.com/Jetup13/onscripter-libretro
    cd onscripter-libretro
    ./update-deps.sh
    mkdir build; cd build; cmake .. && make

### Android
Requirements:
- Android NDK
- CMake (3.9 or later)
- Ninja build system
- 7-Zip (for extracting dependencies on Windows)

Build steps:

    git clone --recursive https://github.com/Jetup13/onscripter-libretro
    cd onscripter-libretro
    
    # Download and extract dependencies
    # Download deps.tar.xz from: https://github.com/Jetup13/onscripter-libretro/releases/download/v0.2/deps.tar.xz
    # Extract with 7-Zip: Right-click -> 7-Zip -> Extract into onscripter-libretro folder
    # Or via command line:
    7z x deps.tar.xz
    7z x deps.tar -odeps
    
    # Copy overlay files
    xcopy /E /I /Y deps.overlay deps
    
    # Make files writable if needed
    attrib -r deps\*.* /s
    
    # Fix source code issues if needed
    powershell -Command "(Get-Content deps/sdl-mixer/dynamic_ogg.h) -replace 'vf, char \*initial', 'vf, const char *initial' | Set-Content deps/sdl-mixer/dynamic_ogg.h"
    powershell -Command "(Get-Content deps/sdl-mixer/music_mad.c) -replace '^\treturn;$', \"`treturn 0;\" | Set-Content deps/sdl-mixer/music_mad.c"
    
    # Fix libmad version file conflict if needed
    del deps\libmad\version
    
    mkdir build-android
    cd build-android
    cmake .. -DCMAKE_TOOLCHAIN_FILE=C:/path/to/ndk/build/cmake/android.toolchain.cmake -DANDROID_ABI=arm64-v8a -DANDROID_PLATFORM=android-21 -DCMAKE_BUILD_TYPE=Release -G Ninja
    ninja
    
    # Optional: Strip debug symbols to reduce file size
    C:/path/to/ndk/toolchains/llvm/prebuilt/windows-x86_64/bin/llvm-strip.exe onscripter_libretro_android.so

Replace `C:/path/to/ndk` with your Android NDK installation path (e.g., `D:/Android/ndk/android-ndk-r27d`).

For other architectures, change `ANDROID_ABI`:
- `arm64-v8a` - 64-bit ARM (most modern devices)
- `armeabi-v7a` - 32-bit ARM
- `x86_64` - 64-bit x86
- `x86` - 32-bit x86
