# KeyInputConversion.h

## 文件概述
将 SDL2 键盘事件（keycode 和 scancode）转换为 ImGui 按键枚举值，实现键盘输入在 ImGui 中的正确映射。

## 核心函数

### `ImGui_ImplSDL2_KeyEventToImGuiKey(keycode, scancode)`
接收 SDL_Keycode 和 SDL_Scancode，返回对应的 ImGuiKey 枚举值。优先匹配 keycode，失败后回退到 scancode 匹配。

## 关键实现逻辑
- 代码移植自 ImGui 官方 SDL2 后端实现（imgui_impl_sdl2.cpp）
- 覆盖所有常用按键：方向键、功能键（F1-F24）、数字键盘、修饰键、字母数字键等
- scancode 回退处理重音符号、括号、反斜杠等依赖键盘布局的按键

## 依赖关系
- `imgui.h` - ImGuiKey 枚举
- `SDL.h` - SDL 键盘事件类型
