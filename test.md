# 测试体系

## 概述

PI-PinBall采用三层测试金字塔体系，参考业界最佳实践构建。

## 测试金字塔

```
                    ╔═══════════════╗
                    ║  性能测试     ║  ← 第三层: 帧率/内存监测
                    ║  Performance  ║
                    ╠═══════════════╣
                    ║  集成测试     ║  ← 第二层: 场景交互/截图验证
                    ║  Integration  ║
                    ╠═══════════════╣
                    ║  单元测试     ║  ← 第一层: 函数/类测试 (最快)
                    ║    Unit       ║
                    ╚═══════════════╝
```

## 工具栈

| 工具 | 用途 | 层级 |
|------|------|------|
| **GUT** | 轻量级GDScript单元测试 | 单元测试 |
| **GdUnit4** | 全能型测试框架(支持模拟/场景测试) | 集成测试 |
| **Screenshot Test** | 视觉回归测试(自研,类似Jest) | 集成测试 |
| **Godot Profiler** | 性能/内存分析 | 性能测试 |

## 目录结构

```
pi-pin-ball/
├── test/
│   ├── unit/                    # 单元测试
│   │   ├── test_game_manager.gd
│   │   ├── test_flipper.gd
│   │   └── test_launcher.gd
│   ├── integration/             # 集成测试
│   │   ├── test_scene_transition.gd
│   │   └── test_scoring.gd
│   ├── screenshot/              # 截图测试
│   │   ├── baseline/            # 基准截图
│   │   ├── current/             # 当前截图
│   │   └── diff/                # 差异图
│   ├── screenshot_test_base.gd   # 截图测试基类
│   ├── run_tests.gd              # 测试运行器
│   └── run_screenshot_tests.sh  # 截图测试脚本
└── doc/
    └── testing_process.md         # 测试流程文档
```

## 测试用例示例

### 单元测试 (GUT)

```gdscript
# test/unit/test_flipper.gd
extends GutTest

func test_flipper_rotation_speed():
    # 验证挡板旋转速度 (Open GDD: 1500度/秒)
    var flipper = Flipper.new()
    flipper.rotation_speed = 1500
    
    assert_eq(flipper.rotation_speed, 1500, "挡板旋转速度应为1500度/秒")
```

### 集成测试

```gdscript
# test/integration/test_scene_transition.gd
extends GutTest

func test_main_menu_to_main_scene_transition():
    var menu = load("res://scenes/ui/MainMenu.tscn").instantiate()
    get_tree().root.add_child(menu)
    await get_tree().process_frame
    
    # 模拟点击开始按钮
    var start_button = menu.get_node("MarginContainer/VBoxContainer/ButtonContainer/StartButton")
    start_button.pressed.emit()
    await get_tree().process_frame
    
    # 验证场景切换
    var current_scene = get_tree().current_scene
    assert_eq(current_scene.name, "Main", "应切换到游戏场景")
    
    menu.free()
```

### 截图测试 (视觉回归)

```gdscript
# test/integration/test_screenshot_main_menu.gd
extends ScreenshotTest

func test_main_menu_visual():
    var menu = load("res://scenes/ui/MainMenu.tscn").instantiate()
    get_tree().root.add_child(menu)
    await get_tree().process_frame
    
    var screenshot = capture_screen("main_menu")
    var result = compare_screenshots("main_menu", screenshot)
    
    assert_true(result.passed, "主菜单截图不匹配")
    menu.free()
```

## 运行测试

### 命令行

```bash
# 运行所有单元测试
godot --headless --path . -s test/run_tests.gd

# 运行截图测试
./test/run_screenshot_tests.sh

# 运行全部测试
./test/run_all_tests.sh
```

### OpenClaw集成

使用browser工具控制Godot进行自动化测试:
1. 启动Godot并打开项目
2. 运行游戏到指定场景
3. 截取屏幕画面
4. 调用截图测试进行对比

## CI/CD集成

```yaml
# .github/workflows/test.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Godot
        uses: heroiclabs/godot-action@v1
        with:
          godot-version: '4.5'
      
      - name: Unit Tests
        run: godot --headless --path . -s test/run_tests.gd
      
      - name: Screenshot Tests
        run: ./test/run_screenshot_tests.sh
      
      - name: Upload Test Results
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: test/screenshot/diff/
```

## 开发流程中的测试

```
┌─────────────────────────────────────────────────────────────┐
│                    开发流程中的测试                          │
├─────────────────────────────────────────────────────────────┤
│  1. 需求分析                                                │
│     → 编写测试用例 (测试驱动开发TDD)                         │
│                                                              │
│  2. 开发实现                                                │
│     → 编写代码 + 单元测试                                    │
│                                                              │
│  3. 集成验证                                                │
│     → 运行集成测试 + 截图测试                               │
│                                                              │
│  4. 提交代码                                                │
│     → 所有测试通过才合并                                    │
│                                                              │
│  5. 持续集成                                                │
│     → CI自动运行全部测试                                    │
└─────────────────────────────────────────────────────────────┘
```

## 对比Jest的视觉测试

| Jest (JavaScript) | Godot (GDScript) |
|-------------------|------------------|
| jest-image-snapshot | ScreenshotTest (自研) |
| toMatchImageSnapshot | compare_screenshots() |
| updateSnapshot | update_baseline() |
| pixelmatch | 自定义像素对比算法 |

## 常用命令速查

| 命令 | 说明 |
|------|------|
| `godot --headless -s test/run_tests.gd` | 运行单元测试 |
| `./test/run_screenshot_tests.sh` | 运行截图测试 |
| `./test/run_all_tests.sh` | 运行全部测试 |

## 控制台测试

轻量级测试方式，通过日志输出验证游戏逻辑:

```bash
# 运行测试并捕获输出
godot --headless --path . -s test/run_tests.gd 2>&1 | tee test.log

# 分析结果
grep -E "✅|❌|错误" test.log
```

详见: `pi-pin-ball/doc/console_test.md`

---

## 四层测试金字塔

```
                    ╔═══════════════╗
                    ║  性能测试     ║  ← 第四层: 帧率/内存监测
                    ║  Performance  ║
                    ╠═══════════════╣
                    ║  截图测试     ║  ← 第三层: 视觉回归测试
                    ║  Screenshot   ║
                    ╠═══════════════╣
                    ║  集成测试     ║  ← 第二层: 场景交互测试
                    ║  Integration  ║
                    ╠═══════════════╣
                    ║  单元测试     ║  ← 第一层: 函数/类测试
                    ║    Unit       ║
                    ╚═══════════════╝
```

**测试模式:** `godot --testmode` - 跳过菜单直接进入游戏

---

## 测试模式

PI-PinBall支持测试模式，用于自动化测试:

```bash
# 测试模式启动 - 跳过菜单直接进入游戏
godot --testmode

# 指定测试场景
godot --testscene=Main

# 无头测试 (无窗口)
godot --headless --testmode
```

详见: `pi-pin-ball/doc/test_mode.md`

---

*更新于: 2026-02-22*
*参考: PI-PinBall项目 test/ 目录*
