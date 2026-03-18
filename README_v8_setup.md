# LVGL v8.3.5 模拟器项目完全指南 (搭建、开发与迁移)

本指南旨在帮助您深入理解 LVGL v8 模拟器的架构，并指导您如何在本项目基础上进行二次开发，以及如何将项目快速部署到其他电脑。

---

## 1. 核心架构与原理 (为什么这样搭建？)

### 1.1 模拟器工作流
1.  **LVGL 核心**: 负责 UI 逻辑、图形渲染计算。
2.  **SDL2**: 负责在 Windows 上开启窗口，并提供像素显示的画布。
3.  **lv_drivers**: 作为 LVGL 和 SDL2 之间的桥梁，将 LVGL 渲染出的像素阵列交给 SDL2 显示。
4.  **CMake/MinGW**: 负责将上述所有 C 语言代码编译成一个可运行的 `.exe`。

---

## 2. 如何编写代码 (二次开发)

所有的业务代码逻辑应主要在 [main.c](file:///d:/Software/DevelopTools/Project/LVGL/lv_port_pc_vscode835/main/src/main.c) 或您新建的源文件中编写。

### 2.1 基础示例：创建一个按钮和标签
在 `main()` 函数的 `while(1)` 循环之前，您可以添加以下代码：

```c
// 1. 创建一个按钮对象
lv_obj_t * btn = lv_btn_create(lv_scr_act());     // 在当前屏幕创建按钮
lv_obj_align(btn, LV_ALIGN_CENTER, 0, 0);         // 居中对齐

// 2. 在按钮上创建一个标签
lv_obj_t * label = lv_label_create(btn);          // 标签的父对象是按钮
lv_label_set_text(label, "Hello LVGL v8!");       // 设置文字
lv_obj_center(label);                             // 在父对象中居中

// 3. 添加事件回调
lv_obj_add_event_cb(btn, [](lv_event_t * e){
    lv_event_code_t code = lv_event_get_code(e);
    if(code == LV_EVENT_CLICKED) {
        LV_LOG_USER("Button clicked!");
    }
}, LV_EVENT_ALL, NULL);
```

### 2.2 开发建议
- **头文件包含**: 始终确保包含 `#include "lvgl/lvgl.h"`。
- **日志调试**: 使用 `LV_LOG_USER("message")` 可以在 VS Code 终端看到输出（需在 `lv_conf.h` 中开启 `LV_USE_LOG`）。
- **文件管理**: 如果添加了新的 `.c` 文件，只需将其放入 `main/src` 目录，CMake 会自动扫描并包含它。

---

## 3. 如何在其他电脑上运行本项目

本项目具有很好的可移植性，但需要注意以下环境依赖：

### 3.1 环境要求
1.  **安装 VS Code**: 必选。
2.  **安装 MinGW-w64**: 建议安装在固定路径（如 `C:/mingw64`）。
3.  **安装 CMake**: 必选。

### 3.2 迁移步骤
1.  **拷贝文件夹**: 将整个 `lv_port_pc_vscode835` 文件夹拷贝到新电脑。
2.  **更新路径 (关键)**: 
    - 打开 `.vscode/tasks.json`，将所有 `d:/Software/DevelopTools/Project/LVGL/mingw64/bin/...` 替换为新电脑上 MinGW 的实际路径。
    - 打开 `.vscode/launch.json`，更新 `miDebuggerPath` 的路径。
    - 打开 `.vscode/c_cpp_properties.json`，更新 `compilerPath`。
3.  **重新配置**:
    - 在新电脑上删除 `build` 文件夹。
    - 按 `Ctrl+Shift+B` 触发 `CMake Configure` 重新生成本地构建文件。

---

## 4. 下次搭建项目的快速清单 (Checklist)

如果您需要再次从零搭建类似项目，请遵循以下检查清单：

1.  下载并部署 LVGL v8.3.5 核心库。
2.  下载并部署 `lv_drivers` 驱动库。

### 实现方法
- 将源码解压到 `lv_port_pc_vscode835` 目录下。
- 确保 `lvgl` 目录下的 [lvgl.h](file:///d:/Software/DevelopTools/Project/LVGL/lv_port_pc_vscode835/lvgl/lvgl.h) 显示版本为 8.3.5。
- [ ] **配置文件**: 准备 `lv_conf.h` 和 `lv_drv_conf.h`（通常从 template 改名而来）。
- [ ] **依赖库**: 确保 `SDL2.dll` 和相关的 `.lib` 文件在正确位置。
- [ ] **CMakeLists.txt**: 
    - 检查 `include_directories` 是否包含所有头文件路径。
    - 检查 `target_link_libraries` 是否链接了 SDL2 库。
- [ ] **代码修复**: 确保 `main.c` 中的线程函数返回类型为 `int` (Windows/SDL2 要求)。
- [ ] **VS Code 配置**: 
    - `tasks.json` 用于自动化 `cmake` 和 `make` 命令。
    - `launch.json` 指向生成的 `.exe` 文件路径。

---

## 5. 常见问题排查

- **Q: 提示找不到 SDL2.h?**
  - A: 检查 `CMakeLists.txt` 中的 `include_directories` 是否包含 `depend/include`。
- **Q: 编译成功但运行报错提示找不到 SDL2.dll?**
  - A: 确保 `SDL2.dll` 已经从 `depend/lib/win64` 复制到了 `build` 目录下。
- **Q: VS Code 代码下面有红色波浪线？**
  - A: 按 `Ctrl+Shift+P` 运行 `C/C++: Reset IntelliSense Database`。
