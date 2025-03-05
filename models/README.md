# TaelinArena 模型制作指南

本文档详细介绍了 TaelinArena 游戏中体素模型的创建、转换和使用流程。

## 模型创建工具

### MagicaVoxel

TaelinArena 的模型主要使用 [MagicaVoxel](https://ephtracy.github.io/) 创建。MagicaVoxel 是一款免费、轻量级的体素编辑器，具有以下特点：

- 简单直观的用户界面
- 强大的编辑和渲染功能
- 支持导出为 `.vox` 格式（TaelinArena 使用的原始格式）
- 支持多种渲染模式和调色板
- 跨平台支持（Windows、Mac、Linux）

### 其他可选工具

虽然 MagicaVoxel 是推荐的主要工具，但任何能够导出 `.vox` 格式文件的体素编辑器也可以使用，例如：

- [Voxel Editor](https://www.voxedit.io/)
- [Goxel](https://goxel.xyz/)
- [Qubicle](https://www.minddesk.com/)

## 目录结构

模型文件按照以下结构组织：

```
models/
├── character_name/              # 角色名称文件夹
│   ├── idle/                    # 静止动画文件夹
│   │   ├── 000.vox             # 第1帧动画的体素模型
│   │   ├── 000.json            # 转换后的优化模型数据
│   │   ├── 001.vox             # 第2帧动画的体素模型
│   │   ├── 001.json            # 转换后的优化模型数据
│   │   └── pivot.json          # 可选的轴心点定义文件
│   ├── walk/                    # 行走动画文件夹
│   ├── attack_name/             # 技能动画文件夹
│   └── effect_name/             # 特效动画文件夹
└── environment/                 # 环境模型文件夹
    └── object_name/             # 环境对象文件夹
```

## 模型创建流程

### 1. 创建新角色

1. 参考 `.github/ISSUE_TEMPLATE/add-new-character.md` 模板设计角色
2. 创建角色的概念草图和参考图
3. 确定角色的动画和技能列表

### 2. 使用 MagicaVoxel 创建模型

1. 打开 MagicaVoxel
2. 创建一个新的体素模型（推荐尺寸：32x32x32 或更小）
3. 使用 MagicaVoxel 的工具创建您的角色模型
4. 对于动画，为每个关键帧创建单独的模型
5. 保存每个帧为单独的 `.vox` 文件，使用从 `000.vox` 开始的数字序列命名

### 3. 设置轴心点（可选）

如果需要自定义轴心点（例如，让角色围绕特定点旋转），可以在动画文件夹中创建 `pivot.json` 文件：

```json
{"x":16,"y":16,"z":0}
```

轴心点坐标是基于 MagicaVoxel 坐标系统的。

### 4. 转换模型

1. 将您的 `.vox` 文件放入适当的文件夹结构中
2. 运行模型转换脚本：
   ```
   npm run build_models
   ```
   
   此脚本将：
   - 将所有 `.vox` 文件转换为优化的 `.json` 格式
   - 更新 `src/models/models.js` 文件，导入所有模型
   - 更新 `src/models/packs.js` 文件
   - 更新 `src/game/TaelinArena.ModelIds.fm` 文件中的模型 ID

## 技术细节

### 转换过程

转换过程会执行以下操作：

1. 解析 `.vox` 文件，提取体素数据和调色板信息
2. 重新计算坐标，以适应游戏的坐标系统
3. 优化模型，移除内部不可见的体素
4. 将模型数据序列化为紧凑的字符串格式
5. 保存为 `.json` 文件

### 优化

转换过程会自动删除模型内部不可见的体素，这可以显著减小模型大小，提高渲染性能。根据模型复杂度，优化可能会移除 30-70% 的体素。

### 动画帧

- 每个动画帧都是单独的 `.vox` 文件
- 帧从 `000.vox` 开始按顺序编号
- 游戏引擎会按照数字顺序循环播放这些帧来创建动画
- 动画速度由游戏逻辑控制

## 最佳实践

1. **保持模型紧凑**：较小的模型加载更快，渲染更高效
2. **使用合适的分辨率**：大多数角色使用 32x32x32 或更小的尺寸
3. **一致的风格**：保持与游戏现有风格一致的颜色和美术风格
4. **动画帧数**：为流畅的动画提供足够的帧数（通常 8-12 帧）
5. **测试优化**：检查转换后的模型大小，对于大型模型考虑进一步优化

## 示例和参考

参考现有角色的模型作为创建新模型的指南：

- `tony_stark/` - 提供了完整的角色动画示例
- `punchingbag/` - 展示了简单角色的基本设置
- `wall/` 和 `poste/` - 提供了环境对象的示例

## 故障排除

- 如果模型没有正确显示，检查坐标和轴心点设置
- 如果转换失败，确保 `.vox` 文件是有效的 MagicaVoxel 格式
- 如果模型闪烁或出现渲染问题，检查模型是否有重叠的体素或边界问题

## 更多资源

- [MagicaVoxel 教程](https://ephtracy.github.io/index.html?page=mv_tutorial)
- [TaelinArena 字符模板](.github/ISSUE_TEMPLATE/add-new-character.md)
- [体素艺术社区资源](https://voxelart.jimdofree.com/tutorials/) 