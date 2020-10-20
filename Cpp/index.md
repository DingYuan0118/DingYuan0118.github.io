# C++学习记录

- [C++学习记录](#c学习记录)
  - [头文件`*.h`与源文件`*.cpp`](#头文件h与源文件cpp)

## 头文件`*.h`与源文件`*.cpp`
- `vscode` 中使用 `g++` 编译文件需要将 `*.h` 文件包含至主程序 `main.cpp` 文件中，由于 `*.h` 文件只包含声明，因此`*.h`文件并不直接参与编译，其具体函数定义存在于 `*.cpp` 文件中。需要手动将 `*.cpp` 文件与 `main.cpp` 文件进行链接，否则编译无法通过，会产生 *undefined reference* 的问题。  
具体操作为在 `task.json` 文件设置中将 `*.cpp` 的路径手动加入至 `g++` 的 `arg` 参数中。例如：

    ```json
    {
        "version": "2.0.0",
        "tasks": [
            {
                "type": "shell",
                "label": "C/C++: g++.exe build active file",
                "command": "C:\\Program Files\\mingw64\\bin\\g++.exe",
                "args": [
                    "-g",
                    "${file}",
                    "-o",
                    "${fileDirname}\\${fileBasenameNoExtension}.exe",
                    "${fileDirname}\\..\\Utilities\\List.cpp"
                ],
                "options": {
                    "cwd": "${workspaceFolder}"
                },
                "problemMatcher": [
                    "$gcc"
                ],
                "group": {
                    "kind": "build",
                    "isDefault": true
                }
            }
        ]
    }
    ```

