# vscode with c++

- [vscode with c++](#vscode-with-c)
  - [Debug](#debug)
    - [References](#references)
    - [vscode in windows + gdb in WSL docker container](#vscode-in-windows--gdb-in-wsl-docker-container)
      - [Debug coredump](#debug-coredump)
      - [Attach to process](#attach-to-process)
  - [C/C++ IntelliSense](#cc-intellisense)
    - [macro define](#macro-define)
      - [c_cpp_properties.json](#c_cpp_propertiesjson)
      - [C/C++ IntelliSense](#cc-intellisense-1)
    - [Include Linux kernel headers](#include-linux-kernel-headers)

## Debug

### References

[windows系统vscode远程调试mysql](http://blog.itpub.net/11566490/viewspace-2670551/)

[vscode如何使用gdb调试](https://www.php.cn/tool/vscode/442575.html)

[Using C++ and WSL in VS Code](https://code.visualstudio.com/docs/cpp/config-wsl)

[Configuring C/C++ debugging](https://code.visualstudio.com/docs/cpp/launch-json-reference)

[Debug C++ in Visual Studio Code](https://code.visualstudio.com/docs/cpp/cpp-debug)

[vscode-debug-specs](https://74th.github.io/vscode-debug-specs/cpp/)

[Is it possible to attach to a remote gdb target with vscode?](https://stackoverflow.com/questions/38089178/is-it-possible-to-attach-to-a-remote-gdb-target-with-vscode)

### vscode in windows + gdb in WSL docker container

- REMOTE EXPLORER
  - Containers
    - Attach to Container
      - EXTENTIONS
        - Install C/C++ IntelliSense, debugging, and code browsing.
      - Run
        - config launch.json

`lauch.json`

    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "(gdb) Launch",
                "type": "cppdbg",
                "request": "launch",
                "program": "${workspaceFolder}/build/src/hello",
                "args": [],
                "stopAtEntry": false,
                "cwd": "${workspaceFolder}",
                "environment": [],
                "externalConsole": false,
                "MIMode": "gdb",
                "setupCommands": [
                    {
                        "description": "Enable pretty-printing for gdb",
                        "text": "-enable-pretty-printing",
                        "ignoreFailures": true
                    }
                ]
            }
        ]
    }

#### Debug coredump

`lauch.json`

    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "(gdb) Launch",
                "type": "cppdbg",
                "request": "launch",
                "program": "<PATH-TO-BINARY>",
                "args": [],
                "stopAtEntry": false,
                "cwd": "${workspaceFolder}",
                "environment": [],
                "externalConsole": false,
                "MIMode": "gdb",
                "setupCommands": [
                    {
                        "description": "Enable pretty-printing for gdb",
                        "text": "-enable-pretty-printing",
                        "ignoreFailures": true
                    }
                ],
                "coreDumpPath": "<PATH-TO-CORE-DUMP>"
            }
        ]
    }

#### Attach to process

            {
                "name": "(gdb) Attach",
                "type": "cppdbg",
                "request": "attach",
                "program": "${workspaceFolder}/a.out",
                "processId": "${command:pickProcess}",
                "MIMode": "gdb",
                "setupCommands": [
                    {
                        "description": "Enable pretty-printing for gdb",
                        "text": "-enable-pretty-printing",
                        "ignoreFailures": true
                    }
                ]
            }

## C/C++ IntelliSense

### macro define

#### c_cpp_properties.json

    F1
    C/C++: Edit Configurations (JSON)

[c_cpp_properties.json reference](https://code.visualstudio.com/docs/cpp/c-cpp-properties-schema-reference)

    {
      "env": {
        "myDefaultIncludePath": ["${workspaceFolder}", "${workspaceFolder}/include"],
        "myCompilerPath": "/usr/local/bin/gcc-7"
      },
      "configurations": [
        {
          "name": "Mac",
          "intelliSenseMode": "clang-x64",
          "includePath": ["${myDefaultIncludePath}", "/another/path"],
          "macFrameworkPath": ["/System/Library/Frameworks"],
          "defines": ["FOO", "BAR=100"],
          "forcedInclude": ["${workspaceFolder}/include/config.h"],
          "compilerPath": "/usr/bin/clang",
          "cStandard": "c11",
          "cppStandard": "c++17",
          "compileCommands": "/path/to/compile_commands.json",
          "browse": {
            "path": ["${workspaceFolder}"],
            "limitSymbolsToIncludedHeaders": true,
            "databaseFilename": ""
          }
        }
      ],
      "version": 4
    }

#### C/C++ IntelliSense

Settings > Extensions > C/C++ > C_Cpp Default: Defines

Edit in settings.json

    {
        "C_Cpp.default.defines": ["__LINUX__", "__cplusplus=201103L"]
    }

### Include Linux kernel headers

- 鼠标移动至带波浪线的 #include

  - 点击 [Quick Fix...]

    - 选择 [Edit "includePath" setting]
      
      工作目录下生成文件 .vscode/c_cpp_properties.json

- 编辑 .vscode/c_cpp_properties.json 如下

      {
          "configurations": [
              {
                  "name": "Linux",
                  "includePath": [
                      "${workspaceFolder}/**",
                      "/usr/src/kernels/3.10.0-1062.el7.x86_64/include/"
                  ],
                  "defines": [],
                  "compilerPath": "/usr/bin/gcc",
                  "cStandard": "c89",
                  "cppStandard": "gnu++98",
                  "intelliSenseMode": "linux-gcc-x64"
              }
          ],
          "version": 4
      }
  
  即, 在 "includePath" 中加入 "/usr/src/kernels/3.10.0-1062.el7.x86_64/include/", 注意于前一条目用逗号分隔

## cmake

[Create a build task](https://github.com/microsoft/vscode-cmake-tools/blob/main/docs/build.md#create-a-build-task)

    > Tasks: Configure task

[How to run Cmake in Visual Studio Code using tasks](https://stackoverflow.com/questions/49584507/how-to-run-cmake-in-visual-studio-code-using-tasks)

[Is it possible to run commands like cmake.build from tasks.json? #1264](https://github.com/microsoft/vscode-cmake-tools/issues/1264)

[How to run Cmake in Visual Studio Code using tasks](https://stackoverflow.com/questions/49584507/how-to-run-cmake-in-visual-studio-code-using-tasks)






