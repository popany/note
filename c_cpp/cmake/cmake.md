# cmake

- [cmake](#cmake)
  - [Examples](#examples)
  - [`Find<package>.cmake` file](#findpackagecmake-file)
  - [Debug](#debug)
  - [Others](#others)
  - [boost](#boost)
  - [windows](#windows)
  - [gtest](#gtest)
  - [Install](#install)
    - [Install from source](#install-from-source)
    - [Install by command](#install-by-command)
  - [Practice](#practice)
    - [`include(GNUInstallDirs)`](#includegnuinstalldirs)
    - [`find_package(Threads REQUIRED)`](#find_packagethreads-required)
    - [What is the idiomatic way in CMAKE to add the -fPIC compiler option?](#what-is-the-idiomatic-way-in-cmake-to-add-the--fpic-compiler-option)

[CMake Useful Variables](https://gitlab.kitware.com/cmake/community/wikis/doc/cmake/Useful-Variables)  

[How to copy DLL files into the same folder as the executable using CMake?](https://stackoverflow.com/questions/10671916/how-to-copy-dll-files-into-the-same-folder-as-the-executable-using-cmake)  

## Examples

[https://cmake.org/examples/](https://cmake.org/examples/)

[awesome-cmake](https://github.com/onqtam/awesome-cmake)

[cmake-examples](https://github.com/ttroy50/cmake-examples)

[learning-cmake](https://github.com/Akagi201/learning-cmake)

## `Find<package>.cmake` file

[Use find_package and Find.cmake modules](https://riptutorial.com/cmake/example/22950/use-find-package-and-find-package--cmake-modules)

[What use is find_package() if you need to specify CMAKE_MODULE_PATH anyway?](https://stackoverflow.com/questions/20746936/what-use-is-find-package-if-you-need-to-specify-cmake-module-path-anyway)

[cmake-examples/01-basic/H-third-party-library](https://github.com/ttroy50/cmake-examples/tree/master/01-basic/H-third-party-library)

[CMake link to external library](https://stackoverflow.com/questions/8774593/cmake-link-to-external-library/41909627#41909627)

## Debug

[CMake debugging](https://cliutils.gitlab.io/modern-cmake/chapters/features/debug.html)

## Others

[OS specific instructions in CMAKE: How to?](https://stackoverflow.com/questions/9160335/os-specific-instructions-in-cmake-how-to)

[How To Write Platform Checks](https://gitlab.kitware.com/cmake/community/-/wikis/doc/tutorials/How-To-Write-Platform-Checks)

[Useful Variables](https://gitlab.kitware.com/cmake/community/-/wikis/doc/cmake/Useful-Variables)

[CMake Project with Third-party Dependencies](https://pmateusz.github.io/c++/cmake/2018/03/11/cmake-project-setup.html)

[CMake target_link_libraries Interface Dependencies](https://stackoverflow.com/questions/26037954/cmake-target-link-libraries-interface-dependencies)

[Listing include_directories in CMake](https://stackoverflow.com/questions/6902149/listing-include-directories-in-cmake)

[CMake: include library dependencies in a static library](https://stackoverflow.com/questions/14199708/cmake-include-library-dependencies-in-a-static-library)

[CMake/Verbose output](https://sidvind.com/wiki/CMake/Verbose_output)

> CMake has a nice colored output which hides the commandline. This is pretty to look at in the long run but sometimes when you write your configurations you want to know if you got all the compiler flags right. There is two ways to disable the pretty output, well, it's essentialy the same but still two different ways.
>
> The first way is to simply run make with the additional argument "VERBOSE=1". This will show each command being run for this session, which is the most useful way to see if the flags is correct.
>
> Code:
>
>       % make VERBOSE=1
>
> The second way is to permanently disable the pretty output in your CMakeLists.txt by setting CMAKE_VERBOSE_MAKEFILE. 
>
> Code: CMakeLists.txt
>
>       set( CMAKE_VERBOSE_MAKEFILE on )
>

[Debug vs Release in CMake](https://stackoverflow.com/questions/7724569/debug-vs-release-in-cmake)

`cmake -DCMAKE_BUILD_TYPE=Debug ..`

[How do I output the result of a generator expression in CMake?](https://stackoverflow.com/questions/51353110/how-do-i-output-the-result-of-a-generator-expression-in-cmake)

[CMake: how to use INTERFACE_INCLUDE_DIRECTORIES with ExternalProject?](https://stackoverflow.com/questions/45516209/cmake-how-to-use-interface-include-directories-with-externalproject)

>
>     # Hack to make it work, otherwise INTERFACE_INCLUDE_DIRECTORIES will not be propagated
>     file(MAKE_DIRECTORY ${XGBOOST_LIB_INCLUDE})

[CMAKE_BUILD_TYPE is not being used in CMakeLists.txt](https://stackoverflow.com/questions/24460486/cmake-build-type-is-not-being-used-in-cmakelists-txt/24470998#24470998)

[How to copy DLL files into the same folder as the executable using CMake?](https://stackoverflow.com/questions/10671916/how-to-copy-dll-files-into-the-same-folder-as-the-executable-using-cmake)

## boost

[How do you add Boost libraries in CMakeLists.txt?](https://stackoverflow.com/questions/6646405/how-do-you-add-boost-libraries-in-cmakelists-txt/6646746#6646746)

[FindBoost](https://cmake.org/cmake/help/v3.6/module/FindBoost.html)

[Whats the proper way to link Boost with CMake and Visual Studio in Windows?](https://stackoverflow.com/questions/18354398/is-it-possible-to-build-boost-with-cmake)

## windows

[CMake: The C Compiler is not able to compile a simple test program](https://stackoverflow.com/questions/53633705/cmake-the-c-compiler-is-not-able-to-compile-a-simple-test-program)

[The CXX compiler identification is unknown](https://stackoverflow.com/questions/20632860/the-cxx-compiler-identification-is-unknown)

[CMakeSettings.json schema reference](https://docs.microsoft.com/en-us/cpp/build/cmakesettings-reference?view=vs-2019)

[MSBuild internals for C++ projects](https://docs.microsoft.com/en-us/cpp/build/reference/msbuild-visual-cpp-overview?view=vs-2019)

[MS C++ 2010 and mspdb100.dll](https://stackoverflow.com/questions/2990331/ms-c-2010-and-mspdb100-dll)

## gtest

[CMake + GoogleTest](https://stackoverflow.com/questions/9689183/cmake-googletest/9695234)

[How to make GTest build /MDd (instead of /MTd) by default, using CMake?](https://stackoverflow.com/questions/12540970/how-to-make-gtest-build-mdd-instead-of-mtd-by-default-using-cmake)

## Install

### Install from source

    yum install gcc-c++
    yum install make
    yum install openssl-devel
    ./bootstrap
    make
    make install

### Install by command

    apt-get update && apt-get install -y wget

    wget -q -O cmake-linux.sh https://github.com/Kitware/CMake/releases/download/v3.16.1/cmake-3.16.1-Linux-x86_64.sh

    sh cmake-linux.sh -- --skip-license --prefix=/usr

    rm cmake-linux.sh

## Practice

### `include(GNUInstallDirs)`

Module `GNUInstallDirs` provides conventions commonly used in GNU-compatible projects.

By including `GNUInstallDirs` a set of variables, which contain installation directories for various artifacts, are provided.

### `find_package(Threads REQUIRED)`

`find_package(Threads)` calls a CMake module that first, searches the file system for the appropriate threads package for this platform, and then sets the `CMAKE_THREAD_LIBS_INIT` variable (and some other variables as well)

- Example 1

      find_package(Threads)
      add_executable(test test.cpp)
      target_link_libraries(test ${CMAKE_THREAD_LIBS_INIT})

- Example 2

      find_package(Threads)
      add_executable(test test.cpp)
      target_link_libraries(test Threads::Threads)

### [What is the idiomatic way in CMAKE to add the -fPIC compiler option?](https://stackoverflow.com/a/38297422)

    set(CMAKE_POSITION_INDEPENDENT_CODE ON)





