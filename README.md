# LVGL v8.3.5 VS Code 模拟器 (SDL2)

这是一个基于 [LVGL v8.3.5](https://github.com/lvgl/lvgl/tree/release/v8.3) 的 PC 模拟器项目，使用 **VS Code** 作为开发环境，**SDL2** 作为图形后端，并通过 **CMake** 进行构建。

---

## 1. 核心架构与原理

### 1.1 模拟器工作流
1.  **LVGL 核心**: 负责 UI 逻辑、图形渲染计算。
2.  **SDL2**: 负责在 Windows/Linux/macOS 上开启窗口，并提供像素显示的画布。
3.  **lv_drivers**: 作为 LVGL 和 SDL2 之间的桥梁，将 LVGL 渲染出的像素阵列交给 SDL2 显示。
4.  **CMake/MinGW**: 负责将上述所有 C 语言代码编译成一个可运行的可执行文件。

---

## 2. 环境搭建 (Environment Setup)

在开始之前，请确保您的电脑已安装以下工具：

1.  **VS Code**: 必选，并安装 `C/C++` 和 `CMake Tools` 扩展。
2.  **编译工具链**:
    - **Windows**: 推荐使用 [MinGW-w64](https://www.mingw-w64.org/) (建议安装在固定路径，如 `C:/mingw64`)。
    - **Linux**: 安装 `gcc`, `g++`, `gdb`, `make`, `cmake`, `libsdl2-dev`。
3.  **CMake**: 必选，版本需在 3.14 及以上。

---

## 3. 快速开始 (Quick Start)

1.  **克隆/下载项目**: 将本项目解压到本地文件夹。
2.  **配置环境路径**:
    - 如果您在 Windows 上使用 MinGW，请检查并更新以下文件中的编译器路径：
        - [.vscode/tasks.json](file:///.vscode/tasks.json)
        - [.vscode/launch.json](file:///.vscode/launch.json)
        - [.vscode/c_cpp_properties.json](file:///.vscode/c_cpp_properties.json)
3.  **构建项目**:
    - 在 VS Code 中打开项目。
    - 按 `Ctrl+Shift+P` 运行 `CMake: Configure`。
    - 按 `Ctrl+Shift+B` 触发构建。
4.  **运行与调试**:
    - 按 `F5` 启动调试。

---

## 4. 二次开发指南 (Development Guide)

所有的业务逻辑代码应主要在 [main/src/main.c](file:///main/src/main.c) 或您新建的源文件中编写。

### 4.1 基础示例：创建一个按钮和标签
在 `main()` 函数的 `while(1)` 循环之前，您可以添加以下代码：

```c
// 1. 创建一个按钮对象
lv_obj_t * btn = lv_btn_create(lv_scr_act());     // 在当前屏幕创建按钮
lv_obj_align(btn, LV_ALIGN_CENTER, 0, 0);         // 居中对齐

// 2. 在按钮上创建一个标签
lv_obj_t * label = lv_label_create(btn);          // 标签的父对象是按钮
lv_label_set_text(label, "Hello LVGL v8!");       // 设置文字
lv_obj_center(label);                             // 在父对象中居中
```

### 4.2 开发建议
- **头文件包含**: 始终确保包含 `#include "lvgl/lvgl.h"`。
- **日志调试**: 使用 `LV_LOG_USER("message")` 可以在 VS Code 终端看到输出（需在 [lv_conf.h](file:///lv_conf.h) 中开启 `LV_USE_LOG`）。
- **文件管理**: 如果添加了新的 `.c` 文件，只需将其放入 `main/src` 目录，CMake 会自动扫描并包含它。

---

## 5. 项目迁移建议 (Migration Guide)

本项目具有很好的可移植性，若要将项目拷贝到其他电脑运行：

1.  **拷贝文件夹**: 将整个项目文件夹拷贝到新电脑。
2.  **更新环境路径 (关键)**: 
    - 确保新电脑已安装 MinGW/CMake。
    - 更新 `.vscode` 文件夹下 json 配置文件中的编译器和调试器路径。
3.  **重新配置**:
    - 在新电脑上删除 `build` 文件夹。
    - 按 `Ctrl+Shift+P` 重新运行 `CMake: Configure`。

---

## 6. 常见问题排查 (Troubleshooting)

- **Q: 提示找不到 SDL2.h?**
  - A: 检查 [CMakeLists.txt](file:///CMakeLists.txt) 中的 `include_directories` 是否包含 `depend/include`。
- **Q: 编译成功但运行报错提示找不到 SDL2.dll?**
  - A: 确保 `SDL2.dll` 已经从 `depend/lib/win64` 自动复制到了 `build` 目录下。
- **Q: VS Code 代码下面有红色波浪线？**
  - A: 按 `Ctrl+Shift+P` 运行 `C/C++: Reset IntelliSense Database`。

---

## 7. 下次搭建项目的快速清单 (Checklist)

1.  下载并部署 LVGL v8.3.5 核心库和 `lv_drivers` 驱动库。
2.  配置文件: 准备 `lv_conf.h` 和 `lv_drv_conf.h`。
3.  依赖库: 确保 `SDL2.dll` 和相关的 `.lib` 文件在正确位置。
4.  CMakeLists.txt: 检查包含目录和库链接。
5.  VS Code 配置: 配置 `tasks.json` 和 `launch.json`。
