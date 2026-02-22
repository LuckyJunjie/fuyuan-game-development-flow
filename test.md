# 测试体系

## 概述

PI-PinBall采用四层测试金字塔体系，参考业界最佳实践构建。

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
              ↕ 控制台测试 (日志输出)
```

## 工具栈

| 层级 | 工具 | 用途 | 下载/安装 |
|------|------|------|----------|
| 第一层 | **GUT** | 轻量级GDScript单元测试 | Godot AssetLib 搜索 "GUT" |
| 第一层 | **GdUnit4** | 全能型测试框架(支持模拟/场景测试) | Godot AssetLib 搜索 "GdUnit4" |
| 第二层 | **集成测试** | 场景交互/输入模拟 | 内置 |
| 第三层 | **GDSnap** | 截图测试/视觉回归 | Godot AssetLib 搜索 "GDSnap" |
| 第三层 | **Screenshot Test** | 自研视觉回归测试 | 内置 (test/screenshot_test_base.gd) |
| 第四层 | **Godot Profiler** | 性能/内存分析 | Godot内置 |
| 控制台 | **Console Log** | 日志输出验证 | 内置 |

## 工具安装指南

### 1. GUT (轻量级单元测试)

**下载:** Godot AssetLib → 搜索 "GUT"  
**安装:** 下载 → 解压到 `res://addons/gut/`

```bash
# 命令行运行测试
godot --headless --path . -s addons/gut/gut_cmdln.gd -testdir=res://test/unit/
```

### 2. GdUnit4 (全能型测试框架)

**下载:** Godot AssetLib → 搜索 "GdUnit4"  
**安装:** 下载 → 解压到 `res://addons/gdUnit4/`

```bash
# 命令行运行
godot --headless --script res://addons/gdUnit4/bin/GdUnitCmdTool.gd --test res://test/
```

## 截图测试的目的

**核心目的：验证代码真正可以运行**

```
截图不是为了对比差异，而是为了证明代码真正可以跑！
- 代码汇报成功 ≠ 实际能运行
- 截图 = 游戏的"存活证明"
```

### 截图时机

1. **首次运行** - 验证游戏能启动
2. **修复Bug后** - 验证问题已解决
3. **新功能开发** - 验证功能正常
4. **CI/CD构建** - 自动化验证

### 截图 vs 视觉回归

| 类型 | 目的 | 工具 |
|------|------|------|
| **运行验证截图** | 证明代码能跑 | 手动截图/broser工具 |
| **视觉回归截图** | 对比差异 | GDSnap/ScreenshotTest |

### 3. GDSnap (截图测试)

**下载:** Godot AssetLib → 搜索 "GDSnap"  
**安装:** 下载 → 解压到 `res://addons/gdsnap/`  
**启用:** 项目 → 项目设置 → 插件 → 启用 GDSnap

```gdscript
# 在测试中使用
var result = GDSnap.take_screenshot("main_menu", get_viewport())
assert_true(result.is_success)
```

### 4. Screenshot Test (自研)

**位置:** `res://test/screenshot_test_base.gd`  
**无需安装:** 项目已内置

```gdscript
extends ScreenshotTest

func test_main_menu():
    var screenshot = capture_screen("main_menu")
    var result = compare_screenshots("main_menu", screenshot)
    assert_true(result.passed)
```

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
│   │   ├── baseline/           # 基准截图
│   │   ├── current/            # 当前截图
│   │   └── diff/               # 差异图
│   ├── screenshot_test_base.gd  # 截图测试基类
│   ├── run_tests.gd             # 测试运行器
│   └── run_screenshot_tests.sh # 截图测试脚本
└── doc/
    ├── testing_process.md       # 测试流程文档
    ├── test_mode.md             # 测试模式文档
    ├── console_test.md          # 控制台测试文档
    └── gdsnap_integration.md    # GDSnap集成文档
```

## 测试用例示例

### 单元测试 (GUT)

```gdscript
# test/unit/test_flipper.gd
extends GutTest

func test_flipper_rotation_speed():
    var flipper = Flipper.new()
    flipper.rotation_speed = 1500
    assert_eq(flipper.rotation_speed, 1500)
```

### 集成测试

```gdscript
# test/integration/test_scene_transition.gd
extends GutTest

func test_main_menu_to_main_scene_transition():
    var menu = load("res://scenes/ui/MainMenu.tscn").instantiate()
    get_tree().root.add_child(menu)
    await get_tree().process_frame
    
    var start_button = menu.get_node("...")
    start_button.pressed.emit()
    await get_tree().process_frame
    
    assert_eq(get_tree().current_scene.name, "Main")
    menu.free()
```

### 截图测试

```gdscript
# test/integration/test_screenshot_main_menu.gd
extends ScreenshotTest

func test_main_menu_visual():
    var menu = load("res://scenes/ui/MainMenu.tscn").instantiate()
    get_tree().root.add_child(menu)
    await get_tree().process_frame
    
    var screenshot = capture_screen("main_menu")
    var result = compare_screenshots("main_menu", screenshot)
    assert_true(result.passed)
    menu.free()
```

### 控制台测试

```bash
# 运行测试并捕获输出
godot --headless --path . -s test/run_tests.gd 2>&1 | tee test.log

# 分析结果
grep -E "✅|❌|错误" test.log
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

# 测试模式 (跳过菜单)
godot --testmode

# 无头测试
godot --headless --testmode
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

## 常用命令速查

| 命令 | 说明 |
|------|------|
| `godot --headless -s test/run_tests.gd` | 运行单元测试 |
| `./test/run_screenshot_tests.sh` | 运行截图测试 |
| `./test/run_all_tests.sh` | 运行全部测试 |
| `godot --testmode` | 测试模式启动 |
| `godot --headless --testmode` | 无头测试 |

---

*更新于: 2026-02-22*
*参考: PI-PinBall项目 test/ 目录*
