---
layout: post
date: 2024-11-13
title: vscode的launch.json
categories:
  - 程序开发
  - 工具
tags:
  - 编程工具
  - vscode
---
`launch.json` 是 VSCode 中的调试配置文件，定义了如何启动和配置调试程序。这个文件中支持使用一些内置变量，以便动态适应不同项目和环境。以下是常用变量的含义和使用方式：

### 1. 基本的内置变量

- **`${workspaceFolder}`**：表示当前打开的工作区目录的路径（也就是项目的根目录）。
  - *示例*：`"${workspaceFolder}/build/output.exe"`

- **`${workspaceFolderBasename}`**：表示工作区文件夹的名称（不包含路径）。
  - *示例*：对于工作区路径 `/home/user/myProject`，`${workspaceFolderBasename}` 会返回 `myProject`。

- **`${file}`**：当前打开文件的路径。
  - *示例*：`"${file}"` 会指向当前文件的绝对路径。

- **`${relativeFile}`**：当前文件相对于工作区目录的相对路径。
  - *示例*：如果工作区为 `/home/user/myProject`，当前文件为 `/home/user/myProject/src/main.cpp`，则 `${relativeFile}` 会是 `src/main.cpp`。

- **`${fileBasename}`**：当前打开文件的名称（包含扩展名，但不含路径）。
  - *示例*：对于文件 `/home/user/myProject/src/main.cpp`，`${fileBasename}` 返回 `main.cpp`。

- **`${fileBasenameNoExtension}`**：当前打开文件的名称（不含路径和扩展名）。
  - *示例*：对于文件 `/home/user/myProject/src/main.cpp`，`${fileBasenameNoExtension}` 返回 `main`。

- **`${fileDirname}`**：当前文件所在目录的路径。
  - *示例*：对于文件 `/home/user/myProject/src/main.cpp`，`${fileDirname}` 返回 `/home/user/myProject/src`。

- **`${fileExtname}`**：当前文件的扩展名（包含点）。
  - *示例*：对于文件 `/home/user/myProject/src/main.cpp`，`${fileExtname}` 返回 `.cpp`。

- **`${cwd}`**：当前工作目录路径。通常指的是 VSCode 的启动目录或终端中的当前目录。
  - *用途*：用于指定调试器的工作目录。

### 2. 任务相关变量

- **`${lineNumber}`**：当前文件中光标所在的行号。
  - *示例*：可以用于调试时直接跳转到光标所在行。

- **`${selectedText}`**：当前选中的文本内容。
  - *用途*：常用于运行时传递选中的代码或参数。

### 3. 用户定义的变量

VSCode 允许在 `settings.json` 中定义用户变量，可以使用 `${config:}` 来引用这些变量。

```json
// settings.json
{
  "myCustomPath": "/path/to/custom/folder"
}
```

在 `launch.json` 中，可以通过以下方式引用：
```json
{
  "program": "${config:myCustomPath}/executable"
}
```

### 4. 环境变量

- **`${env:VARIABLE_NAME}`**：读取系统环境变量 `VARIABLE_NAME` 的值。
  - *示例*：`${env:PATH}` 获取系统的 `PATH` 变量。
  
- **`${command:commandName}`**：可以运行 VSCode 命令并将结果作为变量使用。这些命令包括 `workbench` 和其他插件提供的命令。

### 5. 特殊调试变量

- **`${debugServerPort}`**：调试服务器端口号，适用于远程调试。
- **`${input:variableName}`**：在运行时提示用户输入某个变量的值。
  - *示例*：在需要动态输入参数时很有用。

### 6. `launch.json` 配置示例

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "C++ Launch",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/build/${fileBasenameNoExtension}.exe",
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
      "preLaunchTask": "build",
      "miDebuggerPath": "/usr/bin/gdb",
      "miDebuggerArgs": "-q",
      "logging": {
        "engineLogging": false,
        "trace": false,
        "traceResponse": false
      }
    }
  ]
}
```

### 使用建议
- 可以组合多个变量来动态构建路径或参数，尤其是 `${workspaceFolder}` 和 `${fileBasenameNoExtension}` 的组合。
- 善用 `${env:}` 变量来管理跨平台的环境配置。