# Ordinex (序界) - Docker 视频文件整理工具

一个基于Docker部署的智能视频文件整理系统，能够自动识别、分类和重命名视频文件与STRM播放列表文件。

## 部署命令请看文件 yml  ##

## 下个版本计划 ##
## 🐛 已知问题

暂无

## 版本更新日志

### v2.3.4 (2026-04-14)
- **修复**：STRM 文件去掉 `.strm` 后形如 `天龙八部(97版).20` 的文件名，原先被 `re.sub(r'\.[^.]+$', '')` 误把 `.20` 当扩展名剥掉，导致所有集随机识别成 S01E01；现改为优先匹配末尾 `.数字` 再落入已知视频扩展名剥离，两套兜底路径均修复（缓存命中路径 & 正常识别路径）
- **新增**：目录内存在 ≥2 个纯数字视频文件（如 `1.strm`、`2.strm`…）时，自动用文件夹名去 TMDB 识别并建立剧集缓存，后续每个文件只需提取 stem 数字即可得到对应集号，与原有 SE 标记检测逻辑并行，互不影响

### v2.3.3 (2026-04-14)
- **修复**：剧集文件名仅含纯数字集号（如 `剧名 01.mkv`、`ShowName.02.mkv`）时全部被识别为同一集的问题，六条代码路径全部覆盖（本地整理、云端整理、文件管理识别、识别测试）
- **修复**：云存储整理未调用二级分类功能，现在 `_cloud_format_dest` 已与本地模式对齐，支持 `{{category}}` 命名模板

### v2.3.2 (2026-04-13)
- **修复**：云模式整理后报错 `'NoneType' object is not iterable`——Alist/Openlist 空目录返回 `content: null`，`list_files` 现已正确返回空列表
- **修复**：云模式 move/删除后残留的空目录现在会自动清理
- **修复**：云模式加入文件夹名交叉验证，识别逻辑与本地模式对齐，有效减少不必要的 AI 识别调用


### v2.3.1 (2026-04-13)
- **修复**：云存储模式整理报错 `NamingFormatter is not defined`，现已在 `_cloud_format_dest` 内正确导入
- **修复**：DV（杜比视界）、HDR 等画面效果标识现在会保留到 `videoFormat` 变量中（本地模式和云模式均修复）
- **修复**：图片文件（jpg/png/webp 等）整理时保留原始文件名，不再被重命名为与视频同名（本地模式和云模式均修复）
- **优化**：命名规则输入框旁加注说明：配合二级分类使用时模板须以 `{{category}}/` 开头

### v2.3.0 (2026-04-13)
- **重构**：彻底移除 STRM 生成功能，项目定位为文件整理工具，不再生成额外文件
- **云模式重写**：Alist/Openlist 云模式改为通过 API 执行移动/复制/重命名，与本地模式行为一致
- **新增**：图片/NFO 跟随开关（`extras_enabled`），开启后同名图片和 .nfo 文件随视频一起整理
- **新增**：云模式字幕跟随处理，与本地模式一致
- **优化**：云模式目录缓存写入，同目录多文件整理效率大幅提升
- **修复**：season=None 时格式化字符串崩溃
  
### v2.2.2 (2026-04-13)
- **修复**：filter_keywords 中类似 `[www.bdsee.cn]` 的纯文本关键词不再被当作正则字符类，防止标题被破坏（如 `Bad.Sisters` → `aitr`）

### v2.2.1 (2026-04-12)
- **修复**：文件管理目录缓存 key 对目录使用自身路径，避免同一父目录下不同剧缓存互相污染
- **修复**：filter_keywords 移到截断后应用，防止误删季集号导致标题提取为垃圾字符串
- **修复**：_extract_english 拒绝含技术标签的垃圾结果
- **修复**：季文件夹（S01 无集号）可命中父目录 TV 缓存
- **修复**：中文搜索→英文 TMDB 结果年份精确匹配时给予 7.0 置信度
- **CI**：Docker 镜像标签新增版本号同步，支持手动触发构建

### v2.2.0 (2026-04-11)
- **定时任务**：创建任务时可设置定时执行（单次定时 / 每天 / 每周周期），支持自定义时间
- **定时调度器**：后台 30 秒轮询调度，到期自动触发；运行中任务自动等待完成后再触发，避免冲突
- **首页仪表盘**：新增 Dashboard 首页，展示任务统计、快捷操作入口和最近任务列表，打开不再直接进任务管理
- **首页背景透明**：仪表盘所有模块在有背景图时自动毛玻璃透明化
- **Telegram 智能交互**：支持中文自然语言控制任务，如"看看任务""暂停当前任务""帮我重试上一个失败的"，无需记命令
- **TG 新增命令**：`/pause`、`/resume`、`/cancel` 通过 Telegram 直接暂停、恢复、取消任务
- **AI 意图解析增强**：丰富提示词示例，新增 task_name 字段支持，所有意图（暂停/恢复/取消/帮助）完整映射
- **删除任务自动停止**：删除正在运行的任务时自动取消执行线程，不再后台空跑

### v2.1.0 (2026-04-11)
- **Web UI 优化**：所有帮助说明面板默认折叠，点击「帮助」按钮展开查看，界面更简洁
- **运行日志**：暂停和刷新按钮加大，操作更便捷
- **登录页面重构**：项目 Logo + 名称浮动显示在背景上方，登录卡片改为毛玻璃透明效果
- **侧边栏版本号**：底部显示当前版本号，方便确认版本
- **暂停恢复修复**：任务暂停后点击继续现在能正确恢复执行，不再需要重建任务

### v2.0.0
- 初始发布：任务管理、TMDB/AI 识别、Alist/Openlist 云存储、Telegram 通知、Web UI 管理面板

## 功能特性

✨ **核心功能**
- 📂 支持从A文件夹整理到B文件夹
- 🎬 自动识别电影和电视剧
- 🔍 通过TMDB API对比识别
- 🤖 AI辅助识别（OpenAI API）
- 📝 按自定义规则重新命名文件
- 🔗 支持生成STRM播放列表文件
- 📊 支持创建符号链接或复制文件

🎯 **识别支持**
- 视频格式：MKV, MP4, AVI, MOV, FLV, WMV, M4V, MPG, MPEG, TS, M2TS, MTS等
- STRM播放列表文件识别和处理
- 电影识别（支持年份、多部分识别）
- 电视剧识别（支持季数、集数识别）

🔧 **配置灵活**
- 自定义命名规则（Jinja2模板）
- 环境变量配置
- JSON配置文件支持
- 可调节的识别置信度阈值
- 支持自定义日志级别

🐳 **Docker支持**
- 完整的Docker化部署
- Docker Compose编排
- 开发环境Dockerfile
- 卷挂载方便的文件访问

## 快速开始

### 1. 获取API密钥

#### TMDB API密钥
1. 访问 https://www.themoviedb.org/settings/api
2. 创建账户并申请API密钥
3. 记录你的API密钥

#### OpenAI API密钥
1. 访问 https://platform.openai.com/api-keys
2. 创建新的APIKEY
3. 记录你的密钥

### 2. 初始化项目

```bash
# 复制项目
cp -r video-organizer ~/video-organizer
cd ~/video-organizer

# 运行初始化脚本
bash scripts/setup.sh
```

### 3. 配置应用

**📌 推荐方式（新）：通过 Web UI 配置**

```bash
# 直接启动容器
bash scripts/run.sh

# 打开 Web UI：http://localhost:5000
# 默认登录账号：ord
# 默认密码若未手动设置，会在首次启动时随机生成并写入 web_server.log
# 在 Web 界面中填入 API 密钥和配置
# 点击「保存配置」→ 自动保存到 /config/config.json
```

首次启动如果没有额外配置，Web UI 会启用账号登录。建议尽快通过环境变量设置自己的账号密码与 ADMIN_KEY：

```bash
WEB_AUTH_ENABLED=true
WEB_USERNAME=ord
WEB_PASSWORD=
ADMIN_KEY=replace_with_your_own_admin_key
WEB_SECRET_KEY=replace_with_a_long_random_secret
TG_ENABLED=false
TG_BOT_TOKEN=
TG_CHAT_ID=
TG_NOTIFY_ON_START=false
TG_NOTIFY_ON_COMPLETE=true
TG_NOTIFY_ON_FAILED=true
TG_NOTIFY_ON_CANCELLED=false
```

如果 WEB_PASSWORD 留空，系统会自动生成随机初始密码并写入日志。若配置目录暂时不可写，日志里也会明确提示该密码仅在当前进程内有效。ADMIN_KEY 用于登录页“忘记密码”入口强制重置账号密码。生产环境建议显式设置 WEB_PASSWORD、ADMIN_KEY 和稳定的 WEB_SECRET_KEY。

如需 TG 通知，可在 Web UI 的「系统配置」→「TG 通知」中填写 Bot Token 和 Chat ID，并为任务开始、完成、失败、取消分别设置是否通知。即使关闭 TG_ENABLED，只要保留 Bot Token 和 Chat ID，已授权 Chat 仍可发送 TG 命令。

配置完成后，已授权的 Chat ID 还可以直接给机器人发送命令：

```text
/tasks              查看当前任务和最近可重试任务
/task 任务ID         查看指定任务详情
/retry 任务ID        重试失败/取消/已完成的任务
```

**⚠️ 备选方式（旧）：通过 .env 文件配置**

如果你更喜欢使用环境变量：
```bash
# 复制示例文件并编辑
cp .env.example .env
nano .env
```

编辑以下重要字段：
```
TMDB_API_KEY=your_api_key_here
OPENAI_API_KEY=your_api_key_here
OPENAI_MODEL=gpt-4o-mini
```

### 4. 构建和运行

```bash
# 构建Docker镜像
bash scripts/build.sh

# 运行整理
bash scripts/run.sh
```

## 命名规则

默认命名规则：
```
{{title}}{% if year %} ({{year}}){% endif %}/{{title}}{% if year %} ({{year}}){% endif %}{% if part %}-{{part}}{% endif %}{% if edition%} - {{edition}}{% endif %}{% if videoFormat %} - {{videoFormat}}{% endif %}{% if tmdbid %} [tmdbid={{tmdbid}}]{% endif %}{{fileExt}}
```

### 规则说明

| 变量 | 说明 | 示例 |
|------|------|------|
| `{{title}}` | 影视作品标题 | `The Matrix` |
| `{{year}}` | 发行年份 | `1999` |
| `{{part}}` | 多部分电影的部分号 | `1`, `2` |
| `{{edition}}` | 版本信息 | `Extended Cut`, `Director's Cut` |
| `{{videoFormat}}` | 视频编码格式 | `H.264`, `H.265`, `10-bit` |
| `{{tmdbid}}` | TMDB数据库ID | `603` |
| `{{fileExt}}` | 文件扩展名 | `.mkv`, `.mp4` |
| `{{season}}` | 电视剧季数 | `1`, `2` |
| `{{episode}}` | 电视剧集数 | `01`, `05` |

### 示例输出

**电影：**
```
The Matrix (1999)/The Matrix (1999) - H.264 [tmdbid=603].mkv
```

**电视剧：**
```
Breaking Bad (2008)/Breaking Bad (2008)-S01-E01 - H.264 [tmdbid=1396].mkv
```

## 文件结构

```
video-organizer/
├── src/                          # Python源代码
│   ├── main.py                  # 主程序入口
│   ├── config_loader.py         # 配置加载器
│   ├── tmdb_handler.py          # TMDB API处理
│   ├── ai_recognizer.py         # AI识别器
│   ├── file_organizer.py        # 文件组织逻辑
│   └── utils.py                 # 工具函数
├── config/
│   └── config.json              # 默认配置文件
├── scripts/
│   ├── setup.sh                 # 初始化脚本
│   ├── build.sh                 # 构建脚本
│   ├── run.sh                   # 运行脚本
│   └── remove.sh                # 清理脚本
├── input/                        # 输入文件夹（挂载）
├── output/                       # 输出文件夹（挂载）
├── logs/                         # 日志文件夹（挂载）
├── Dockerfile                    # 生产环境Dockerfile
├── Dockerfile.dev               # 开发环境Dockerfile
├── docker-compose.yml           # Docker编排文件
├── requirements.txt             # Python依赖
├── .env.example                 # 环境变量示例
├── .gitignore                   # Git忽略文件
└── README.md                    # 本文件
```

## Docker Compose 配置详解

### 卷挂载

```yaml
volumes:
  - ./input:/input        # 源文件夹
  - ./output:/output      # 目标文件夹
  - ./config:/config      # 配置文件夹
  - ./logs:/logs          # 日志文件夹
```

### 环境变量

```yaml
environment:
  SOURCE_DIR: /input
  DEST_DIR: /output
  TMDB_API_KEY: ${TMDB_API_KEY}
  OPENAI_API_KEY: ${OPENAI_API_KEY}
  LOG_LEVEL: INFO
  MIN_CONFIDENCE: "0.6"
```

## 工作流程

```
1. 扫描源文件夹 (SOURCE_DIR)
   ↓
2. 逐个处理文件
   ├── 识别文件名和文件夹名
   ├── 查询TMDB API
   └── 如果置信度低 → 调用AI辅助识别
   ↓
3. 格式化新文件名
   ├── 应用命名规则
   └── 生成目标路径
   ↓
4. 组织文件
   ├── 移动/复制文件到目标位置
   └── 生成STRM播放列表（可选）
   ↓
5. 输出统计信息
```

## AI识别工作流程

当TMDB识别置信度低于 `MIN_CONFIDENCE` 时：

1. 提取文件名中的关键信息（标题、年份、季数、集数等）
2. 调用OpenAI AI进行内容识别
3. AI返回识别结果（影视类型、官方标题、发行年份等）
4. 如果AI识别置信度足够，使用AI结果
5. 否则记录为失败并跳过该文件

## 配置文件详解

### config.json 配置示例

```json
{
  "source_dir": "/input",
  "dest_dir": "/output",
  "tmdb_api_key": "your_key",
  "openai_api_key": "your_key",
  "openai_model": "gpt-4o-mini",
  "naming_pattern": "{{title}}...",
  "strm_enabled": true,
  "ai_enabled": true,
  "log_level": "INFO",
  "min_confidence": 0.6
}
```

### 环境变量和配置优先级

**配置加载顺序（优先级从高到低）：**

1. **Web UI 配置** (最高) - `/config/config.json` 中的配置
   - 通过 Web 界面保存的配置
   - 自动写入文件，容器重启后保留
   
2. **环境变量** - docker-compose.yml 或系统环境中设置
   - 如果没有 config.json，从环境变量读取
   
3. **默认值** (最低) - 代码中的硬编码默认值

**这意味着：** Web UI 的配置会覆盖所有环境变量，确保你在 Web 界面保存的设置持久生效。

## Web UI 配置界面

### 访问 Web 界面

```bash
# 启动容器后，打开浏览器访问
http://localhost:5000
```

### 配置特性

✅ **实时配置修改** - 无需重启容器
✅ **自动持久化** - 配置自动保存到  `/config/config.json`
✅ **容器重启保留** - 配置在容器重启后仍然存在
✅ **直观界面** - 清晰的 Web UI 配置面板

### 配置流程

1. 打开 Web UI：http://localhost:5000
2. 导航到「系统配置」或「基础配置」
3. 填入 TMDB API 密钥、OpenAI API 密钥等
4. 调整其他参数（日志级别、置信度阈值等）
5. 点击「保存配置」
6. 看到「配置已保存到文件」消息 ✅

### 配置持久化说明

📌 **新特性：配置自动保存**

当你在 Web UI 中保存配置时：
```
Web UI 表单
  ↓
点击「保存配置」按钮
  ↓
/api/config POST 请求
  ↓
ConfigLoader.update() 自动保存
  ↓
/config/config.json 文件更新
  ↓
容器重启自动加载此配置
```

详细信息参看：[CONFIG_PERSISTENCE.md](CONFIG_PERSISTENCE.md)

## 日志系统

日志文件位置：`./logs/organizer.log`

### 日志级别

- `DEBUG` - 详细调试信息
- `INFO` - 一般信息
- `WARNING` - 警告信息
- `ERROR` - 错误信息

### 查看日志

```bash
# 实时查看日志
tail -f logs/organizer.log

# 查看最后100行
tail -n 100 logs/organizer.log

# 搜索特定内容
grep "ERROR" logs/organizer.log
```

## 使用示例

### 示例1：整理电影集合

准备源文件：
```
./input/
├── The.Matrix.1999.1080p.mkv
├── The.Matrix.Reloaded.2003.mkv
└── Inception.2010.x265.mkv
```

运行整理后：
```
./output/
├── The Matrix (1999)/
│   └── The Matrix (1999) - H.264 [tmdbid=603].mkv
├── The Matrix Reloaded (2003)/
│   └── The Matrix Reloaded (2003) - H.264 [tmdbid=234215].mkv
└── Inception (2010)/
    └── Inception (2010) - H.265 [tmdbid=27205].mkv
```

### 示例2：整理电视剧

准备源文件：
```
./input/
├── Breaking.Bad.S01E01.Pilot.mkv
├── Breaking.Bad.S01E02.Cat's.in.the.Bag.mkv
└── Breaking.Bad.S02E01.Seven.Thirty-Seven.mkv
```

运行整理后：
```
./output/
└── Breaking Bad (2008)/
    ├── Breaking Bad (2008)-S01-E01 [tmdbid=1396].mkv
    ├── Breaking Bad (2008)-S01-E02 [tmdbid=1396].mkv
    └── Breaking Bad (2008)-S02-E01 [tmdbid=1396].mkv
```

## 故障排除

### 1. "TMDB API密钥无效"

```
错误信息: Error searching TMDB for movie 'xxx': ...
```

✅ 解决方案：
- 检查API密钥是否正确
- 确认API密钥有效期未过期
- 访问 https://www.themoviedb.org/settings/api 重新验证

### 2. "OpenAI API错误"

```
错误信息: Error in AI recognition: ...
```

✅ 解决方案：
- 检查OpenAI API密钥
- 确认账户余额充足
- 检查网络连接

### 3. "文件权限错误"

```
错误信息: Permission denied: ...
```

✅ 解决方案：
```bash
# 修改目录权限
chmod 755 ./input
chmod 755 ./output
```

### 4. "Docker构建失败"

```
错误信息: failed to build image
```

✅ 解决方案：
```bash
# 清理旧容器
docker system prune -a

# 重新构建
bash scripts/build.sh
```

### 5. "识别失败率高"

✅ 解决方案：
- 调整 `MIN_CONFIDENCE` 值（更低则更多使用AI识别）
- 确保文件名清晰
- 检查TMDB中是否存在改标题的作品

### 6. "Web UI 配置界面显示空白"

```
问题描述: 打开Web界面后，配置选项卡显示为空白
```

✅ 快速解决：
- **方案A: 使用 API 测试工具**（推荐）
  ```bash
  # 有两种方式测试API
  
  # 1. 打开浏览器访问 HTML 测试工具（需要正确指向服务器地址）
  # 将 api_tester.html 文件放在可访问的位置，然后在浏览器中打开
  
  # 2. 使用 Python 脚本测试
  python debug_api.py http://your-server:5000
  ```

- **方案B: 按照调试指南排查**
  详见 [DEBUG_WEB_UI.md](DEBUG_WEB_UI.md)
  
- **方案C: 快速步骤**
  1. 打开浏览器的开发者工具（F12）
  2. 进入 Console 标签页
  3. 查看是否有红色错误信息
  4. 如果看到 "Loading config..." 日志，说明 JavaScript 正在运行
  5. 检查 Network 标签中的 `/api/config` 请求是否返回 200 状态码
  6. 如果返回 200，说明 API 正常，问题在前端；否则检查服务器日志

## 高级使用

### 自定义命名规则

编辑 `config/config.json` 中的 `naming_pattern` 字段：

```json
{
  "naming_pattern": "{{title}} ({{year}}) - {{edition}}/{{title}} ({{year}}) - {{edition}} [S{{season|string}}E{{episode|string}}]{{fileExt}}"
}
```

### 多源标签支持

可以在命名规则中添加更多字段：
```json
{
  "naming_pattern": "[{{tmdbid}}] {{title}} ({{year}})/{{title}}{{fileExt}}"
}
```

### 跳过特定文件格式

编辑 `supported_video_formats`：
```json
{
  "supported_video_formats": [".mkv", ".mp4"]
}
```

### 禁用STRM文件生成

编辑 config 或环境变量：
```bash
# 在 .env 中（需要重新编译）
STRM_ENABLED=false
```

## 开发指南

### 使用开发环境

```bash
# 使用开发Dockerfile
docker-compose -f docker-compose.yml build --build-arg DOCKERFILE=Dockerfile.dev

# 进入容器进行开发
docker-compose exec video-organizer /bin/bash
```

### 运行测试

```bash
docker-compose exec video-organizer pytest
```

### 代码格式化

```bash
docker-compose exec video-organizer black src/
docker-compose exec video-organizer flake8 src/
```

## 性能优化

### 1. 大文件处理

对于大量文件（>1000个），建议：
- 增加容器内存限制
- 分批处理文件
- 使用异步处理

### 2. API速率限制

- TMDB API：每秒40个请求
- OpenAI API：根据账户等级限制

### 3. 网络优化

- 启用本地DNS缓存
- 使用更快的网络连接

## 常见问题

**Q: 支持哪些视频格式？**
A: MKV, MP4, AVI, MOV, FLV, WMV, M4V, MPEG, TS, M2TS, MTS, WebM, 3GP, OGV 等常见格式。

**Q: 是否支持Windows？**
A: 支持。使用 `scripts/run.ps1` (PowerShell)或直接 `docker-compose up`。

**Q: 可以同时处理多个源文件夹吗？**
A: 可以。修改docker-compose.yml配置多个卷挂载。

**Q: 如何备份原始文件？**
A: 默认behavior是移动文件。如果需要备份，在docker-compose.yml中修改脚本使用copy操作。

**Q: 支持云存储整合吗？**
A: 目前仅支持本地文件系统。可以通过挂载网络存储来实现。

## 更新和维护

### 更新项目

```bash
git pull origin main
bash scripts/build.sh
```

### 清理和重置

```bash
# 移除所有容器和镜像
bash scripts/remove.sh

# 完全重置项目
rm -rf input/ output/ logs/
bash scripts/setup.sh
```

## 贡献指南

欢迎提交Issue和Pull Request！

## 许可证

MIT License

## 联系方式

如有问题或建议，请提交Issue。

---

**最后更新**: 2026-04-12
**项目版本**: 2.2.1
