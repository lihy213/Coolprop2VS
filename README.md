# Coolprop2VS

This doc describes the steps for facilitate Coolprop package to Visual Studio.

**Test ENV: Visual Studio 2019 Community, Windows 10.**

## Tools

1. Download and install `VS2019 Community`: [Downloads & Keys - Visual Studio Subscriptions](https://my.visualstudio.com/Downloads?q=visual\studio\2019&wt.mc_id=o~msft~vscom~older-downloads)
2. Download `cmake`: [Download CMake](https://cmake.org/download)；[Windows下CMake安装使用_cmake window-CSDN博客](https://blog.csdn.net/finghting321/article/details/105528436)

## CoolProp Setup

```c++
# Check out the sources for CoolProp
git clone https://github.com/CoolProp/CoolProp --recursive  //下载
# Move into the folder you just created
cd CoolProp
# Make a build folder
mkdir -p build && cd build      //创建build子文件夹
# Build the makefile using CMake
cmake .. -DCOOLPROP_STATIC_LIBRARY=ON
# Make the static library
cmake --build .
```

In line 8, the parameters should match IDE, e.g., for VS2019 are:

```c++
cmake .. -A x64 -G "Visual Studio 16 2019" -DCOOLPROP_STATIC_LIBRARY=ON
```
For VS2022, are:

```c++
cmake .. -DCOOLPROP_STATIC_LIBRARY=ON -G "Visual Studio 17 2022" -A x64
```

**TIPS**

If it occurs problem like  *error C1128: 节数超过对象文件格式限制: 请使用 /bigobj 进行编译* when build line 8, you could try to open ` .vcxproj` in `CoolProp/build` and then find the parameters:
```c++
<ItemDefinitionGroup Condition="'$(Configuration)|$(Platform)'=='Debug|x64'">
  <ClCompile>
    <AdditionalOptions>/bigobj %(AdditionalOptions)</AdditionalOptions>
  </ClCompile>
</ItemDefinitionGroup>
```
add ` /bigobj` parameter into it.


## Link to CoolProp

1. Build a project file, here take the `main file` as an example,

   1. ![image-20251222211545298](./README.assets/image-20251222211545298.png)

   2. ```c++
      main
       |- CMakeLists.txt (For your project, see below)
       |- mycode.cpp
       |- externals
          |- CoolProp
              |- src
              |- include
              |- ...
              |- CMakeLists.txt
              |-
      ```

      - Add the following lines to `CmakeLists.txt`:

      - ```c++
        # See also http://stackoverflow.com/a/18697099
        cmake_minimum_required (VERSION 2.8.11)
        project (main)
        set(COOLPROP_STATIC_LIBRARY true)
        add_subdirectory ("${CMAKE_SOURCE_DIR}/externals/CoolProp" CoolProp)
        add_executable (main "${CMAKE_SOURCE_DIR}/mycode.cpp")
        target_link_libraries (main CoolProp)
        ```

      - Utilize `cmake-gui` to realize the link between Coolprop and VS2019:

      - ![image-20251222211756327](./README.assets/image-20251222211756327.png)

      - ![image-20251222211801444](./README.assets/image-20251222211801444.png)![image-20251222211805127](./README.assets/image-20251222211805127.png)

DONE.
