# 🛍️ taobao-visual-studio

> 淘宝 / 天猫主图 + 详情页 **视觉策划与生图 Agent Skill**。上传商品图、说一句话，就能拿到一套可上架的 5 张主图 + 8-12 屏详情页策划、逐张 Prompt，可选直接出图，并自动检查平台规范和《广告法》违禁词。
>
> 兼容京东 / 拼多多 / 抖音电商 · 支持 Claude Code / Codex / GitHub Copilot CLI / 任何支持 Agent Skills 的工具

- [它能做什么](#它能做什么)
- [快速开始](#快速开始)
- [安装](#安装)
- [使用方法](#使用方法)
- [直接出图配置（可选）](#直接出图配置可选)
- [自检脚本](#自检脚本)
- [目录结构](#目录结构)
- [常见问题](#常见问题)

## 它能做什么

| 能力 | 说明 |
|---|---|
| 🧠 卖点策划 | 从商品图自动识别品类/材质/结构，补全卖点，生成**消费者卖点地图**和**购买理由卡**，判断靠颜值/痛点/情感成交 |
| 🗂️ 统一策划 | 5 张主图 + 8-12 屏详情页**一张表一次审批**：每张的任务、画面、角度、背景、文案 |
| 🎨 风格一致 | 10 字段 **Style Lock** 锁死整套图的色板/字体/光线；6 套中文风格预设（含国风） |
| ✍️ 逐张 Prompt | 5 段式结构 + 生图铁律（hex 色、产品占比、留白、避让区、中文「」文案） |
| 🖼️ 直接出图 | 支持 Agent 内置生图工具或任意 OpenAI 兼容接口；以商品原图做参考，防止产品跑偏 |
| ✅ 上架自检 | 白底图/尺寸/文件大小检查并一键导出合规尺寸；《广告法》极限词 + 化妆品/食品等类目禁用词检查 |

工作流：

```
读图&补全信息 → 卖点地图 + 驱动力诊断 → 产品身份锁定 → 统一策划表【你审批】
→ 风格锁 + 逐张 Prompt → 出图 → 自检（规范 / 违禁词 / 视觉）→ 交付
```

## 快速开始

```bash
git clone https://github.com/wbin888/taobao-image2.git ~/.claude/skills/taobao-visual-studio
```

重启 Claude Code，上传商品图后说：

```text
用 taobao-visual-studio 帮我做一套淘宝主图和详情页
```

## 安装

仓库根目录就是 skill 目录，克隆到你所用工具的 skills 目录即可（任选其一）：

```bash
# Claude Code
git clone https://github.com/wbin888/taobao-image2.git ~/.claude/skills/taobao-visual-studio

# GitHub Copilot CLI
git clone https://github.com/wbin888/taobao-image2.git ~/.copilot/skills/taobao-visual-studio

# Codex
git clone https://github.com/wbin888/taobao-image2.git ~/.codex/skills/taobao-visual-studio

# 通用 Agent Skills 目录
git clone https://github.com/wbin888/taobao-image2.git ~/.agents/skills/taobao-visual-studio
```

> 仓库名是 `taobao-image2`，但本地目录请保持 `taobao-visual-studio`（与 SKILL.md 中的 name 一致）。

**只在某个项目里用**：克隆到项目下的 `.claude/skills/taobao-visual-studio`（或 `.github/skills/`、`.agents/skills/`）。

**Windows（PowerShell）**：

```powershell
git clone https://github.com/wbin888/taobao-image2.git "$env:USERPROFILE\.claude\skills\taobao-visual-studio"
```

**没装 git**：点页面右上角 **Code → Download ZIP**，解压后把文件夹改名为 `taobao-visual-studio`，放进上面的 skills 目录。

**更新**：

```bash
cd ~/.claude/skills/taobao-visual-studio && git pull
```

**可选依赖**（只有运行自检脚本才需要）：

```bash
pip install Pillow    # check_image.py 用；lint_copy.py / generate_image.py 只需 Python 3.9+
```

安装后**重启工具**。验证：在 Claude Code 输入 `/skills`，或在 Copilot CLI 中问「你有哪些 skills」，能看到 `taobao-visual-studio` 即成功。

## 使用方法

### 1. 准备素材
- **商品图**（必需，1-3 张，正面清晰最好；有包装/背面/细节图更佳）
- 卖点、参数、目标人群、品牌色、价格带、检测报告等（可选，没有就让它从图里推断）

### 2. 选一种说法

| 你想要 | 这样说 |
|---|---|
| 完整策划（只要方案和 Prompt） | `用 taobao-visual-studio 帮这款保温杯做淘宝主图和详情页方案` |
| 策划 + 直接出图 | `用 taobao-visual-studio 给这个商品做一套天猫主图和 10 屏详情页，直接出图` |
| 只做白底图 | `用 taobao-visual-studio 把这张图做成淘宝白底图` |
| 只做主图 | `用 taobao-visual-studio 只做 5 张主图，3:4 竖版，风格用国风雅致` |
| 已有详情页结构 | `按我这个 10 屏结构优化每屏的画面和文案：…` |
| 其他平台 | `做一套拼多多主图` / `抖音电商主图和详情页` |

### 3. 跟着它走
1. 只上传了图时，它会**问一次**有没有补充资料，回「没有，你推断」即可
2. 它输出**卖点地图 + 驱动力诊断 + 统一策划表**，你可以改文案、换画面、删屏
3. 回复「确认」后，它锁定风格并写出每张 Prompt（或直接出图）
4. 出图后自动自检，有问题只改最严重的一处再重生
5. 交付：文件路径、验证报告、假设清单

> 想跳过审批：在请求里加「跳过审核，直接出图」。

### 4. 输出位置（出图模式）

```
taobao-visual/20261102-2130-保温杯/
├── 主图/        01-05.png（第 5 张为白底图）
├── 详情页/      01-10.png
├── prompts/     每张图的 Prompt
└── 记录/        验证报告.md
```

## 直接出图配置（可选）

按优先级自动选择：

1. **Agent 自带生图工具**（如 Codex imagegen）→ 无需配置
2. **OpenAI 兼容接口** → 设置环境变量：

   ```bash
   export IMAGE_API_KEY=sk-xxx                       # 必填（也兼容 OPENAI_API_KEY）
   export IMAGE_API_BASE=https://api.openai.com/v1   # 可选，中转/云厂商网关填对应地址
   export IMAGE_MODEL=gpt-image-1                    # 可选
   ```
3. **都没有** → 交付完整 Prompt 包，复制到 ChatGPT / 即梦 / 豆包 / Midjourney 等手动出图

> API Key 只放在本地环境变量，skill 不会索要、写入或回显。批量出图前会先告诉你张数并确认。

## 自检脚本

也可以单独使用：

```bash
S=~/.claude/skills/taobao-visual-studio/scripts

# 白底图：纯白背景、主体占比、是否被裁切
python3 $S/check_image.py 白底.png --role white

# 主图尺寸/比例/大小（--fix 导出 800x800 合规 JPG 副本）
python3 $S/check_image.py 主图/*.png --role main --fix

# 详情页：导出 750 宽（天猫用 --platform tmall 导出 790 宽）
python3 $S/check_image.py 详情页/*.png --role detail --fix

# 《广告法》违禁词（可加 --category cosmetics|food|health|baby|medical）
python3 $S/lint_copy.py "全网第一的保温杯，最强保温"
python3 $S/lint_copy.py -f 文案.md --category cosmetics

# 生图（单张）
python3 $S/generate_image.py --prompt-file prompt.txt --size 1024x1024 --image 商品.png --out 主图/01.png
```

支持平台：`taobao`（默认）/ `tmall` / `jd` / `pdd` / `douyin`。

## 目录结构

```
taobao-visual-studio/
├── SKILL.md                    # 主流程（Agent 读取）
├── references/
│   ├── taobao-specs.md         # 各平台主图/白底图/详情页尺寸规范
│   ├── sequences.md            # 卖点地图、主图 5 张序列、详情页序列、品类适配
│   ├── prompt-craft.md         # 5 段式、Style Lock、生图铁律、角度景别、去 AI 感、6 套风格
│   ├── compliance.md           # 《广告法》违禁词、特殊类目、AIGC 标识
│   └── source-analysis.md      # 参考的 5 个开源 Skill 分析
└── scripts/
    ├── check_image.py          # 白底/尺寸检查与导出
    ├── lint_copy.py            # 违禁词检查
    └── generate_image.py       # OpenAI 兼容生图
```

## 常见问题

**Q：没有生图 API 能用吗？**
能。默认就是策划模式，输出策划表 + 全部 Prompt，复制到任何生图工具即可。

**Q：生成的产品和实物不一样？**
提供清晰的商品原图，skill 会把原图作为参考图并写保留清单；角度不全时会先生成一张「角度锁定图」让你确认。

**Q：图里中文出现错字？**
放大逐字核对；skill 会尽量用简单字，并在多次失败后改为「无字底图 + 后期排字」。

**Q：文案说「最好」「第一」可以吗？**
不可以，属于《广告法》绝对化用语。skill 会自动检查并改写为可证明的具体表达。词表检查不能替代平台审核，上架前以平台后台为准。

**Q：会编造检测报告、销量、好评吗？**
不会。没有证据时用「【检测报告占位】」等占位，由你补充真实材料。

**Q：尺寸规范准确吗？**
按常用规范整理，类目可能有额外要求，上架前以卖家后台最新规则为准。

## 致谢

设计参考了 [liangdabiao/ecom-details-image](https://github.com/liangdabiao/ecom-details-image)、[coolqoo/1click-ecom-detailpage](https://github.com/coolqoo/1click-ecom-detailpage)、[wzj177/ecommerce-image-suite](https://github.com/wzj177/ecommerce-image-suite)、[buluslan/gpt-image2-ecommerce](https://github.com/buluslan/gpt-image2-ecommerce)、[oldred-byte/ec-visual-skill](https://github.com/oldred-byte/ec-visual-skill) 的思路，内容与脚本为重新编写。
