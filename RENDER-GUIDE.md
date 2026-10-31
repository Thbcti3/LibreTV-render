# LibreTV 部署到 Render（小白版 · 一步一步照做）

> 目标：把这个文件夹变成一个公网网址 `https://xxxx.onrender.com`，手机电脑都能打开，**不依赖你的电脑**。
> 全程网页操作，只有一个地方要装个小软件（也可以用网页版替代）。
> 预计耗时：**30 分钟**（其中 15 分钟是等服务器构建，你只需要看着）。

---

## 第 0 步：先搞清楚你要上传的是什么

要上传的文件夹是：

```
D:\小工具\LibreTV-render
```

里面已经有：

| 文件/文件夹 | 作用 |
|---|---|
| `src/`、`public/` | 网站的全部代码和图片 |
| `Dockerfile` | 告诉 Render 怎么把它跑起来 |
| `render.yaml` | 告诉 Render 用免费套餐、新加坡机房、以及密码等配置 |
| `sources.json` | 100 个点播源 |
| `start.js` | 启动脚本 |

**不要动里面的文件**，尤其别删 `Dockerfile` 和 `render.yaml`。

---

## 第 1 步：注册 GitHub 账号（5 分钟）

1. 打开 <https://github.com/signup>
2. 依次填：邮箱 → 密码（至少 8 位，含数字）→ 用户名（英文+数字，比如 `laiyan2026`）
3. 会做一次「拼图验证」（旋转图片让动物正过来），照做即可
4. 收邮件，输入 8 位验证码
5. 出现「Welcome to GitHub」页面 = 注册成功

> 如果 GitHub 打不开或很慢，挂个梯子再试，或者换手机热点。

---

## 第 2 步：把代码传上去（二选一）

### 方式 A：GitHub Desktop（推荐，最省事）

1. 打开 <https://desktop.github.com/> 下载安装（Windows 版）
2. 打开软件 → 用刚才的账号登录
3. 菜单栏 `File` → `Add local repository...`
4. 选择文件夹：`D:\小工具\LibreTV-render`
5. 底部会提示「这不是 Git 仓库，是否创建？」→ 点 **create a repository**
6. 点 **Create repository**（不用改任何选项）
7. 右上角点 **Publish repository**（发布到 GitHub）
   - Name 填 `libretv`
   - **把 "Keep this code private" 的勾去掉**（Render 免费版要读公开仓库）
   - 点 **Publish repository**
8. 等几秒，提示发布成功即可

### 方式 B：网页上传（不想装软件）

GitHub 网页一次最多传 100 个文件，所以要分几次：

1. 打开 <https://github.com/new>
   - Repository name 填 `libretv`
   - 选 **Public**
   - 点 **Create repository**
2. 点页面上的 **uploading an existing file**
3. 分 5 次拖拽上传，**每拖完一次点一次绿色的 Commit changes**：

| 顺序 | 拖进去的文件夹/文件 |
|---|---|
| 1 | `src\lib`（整个文件夹） |
| 2 | `src\components` |
| 3 | `src\app` |
| 4 | `public` |
| 5 | 根目录剩下的所有文件（`Dockerfile`、`render.yaml`、`package.json`、`sources.json`、`start.js` 等） |

> ⚠️ 第 5 步的根目录文件必须拖到仓库**最外层**，不要拖进 `src` 里面。
> ⚠️ 上传后仓库首页应该能看到 `Dockerfile` 和 `render.yaml` 这两个文件，看不到就是传错位置了。

---

## 第 3 步：注册 Render（3 分钟）

1. 打开 <https://dashboard.render.com/>
2. 点 **GitHub** 按钮（用 GitHub 账号登录，不用另设密码，也不用绑卡）
3. 授权页面点 **Authorize Render**
4. 进入控制台

---

## 第 4 步：部署（两种方式，先试 Blueprint）

### 方式 A：Blueprint（自动读配置，推荐）

1. 右上角点 **New +**
2. 选 **Blueprint**
3. 找到 `libretv` 仓库 → 点 **Connect**
4. Render 会自动识别仓库里的 `render.yaml`，显示一个名为 `libretv` 的服务
5. 点 **Apply**（或 **Create Resources**）
6. 开始构建，跳到下面的「第 5 步」

### 方式 B：手动建 Web Service（Blueprint 报错时用）

1. **New +** → **Web Service**
2. 选 `libretv` 仓库 → **Connect**
3. 填写：
   - Name：`libretv`
   - Region：**Singapore**
   - Branch：`main`
   - Runtime：**Docker**
   - Instance Type：**Free**
4. 点 **Create Web Service**
5. 到 **Environment** 标签页，逐条 **Add Environment Variable**：

| Key | Value |
|---|---|
| `PASSWORD` | `13622` |
| `ADMIN_USER` | `1362280` |
| `ADMIN_PASSWORD` | `Aa230702` |
| `DEFAULT_RECOMMEND_SOURCE` | `douban` |
| `SEARCH_MAX_PAGES` | `1` |
| `SEARCH_SOURCE_TIMEOUT_MS` | `5000` |
| `SEARCH_CONCURRENCY` | `20` |

6. 保存后 Render 会自动重新部署

---

## 第 5 步：等构建（8～15 分钟，你只需要看着）

- 页面下方会滚动 **Logs**
- 依次会看到：`Cloning` → `npm ci` → `next build` → `Successfully built` → **Live**
- **只要最后显示 `Live`（绿色）就成功了**
- 中间出现黄色 warning 不用管，只有红色的 `error` 才需要处理

---

## 第 6 步：拿到网址并验证

页面顶部会显示形如：

```
https://libretv-a1b2.onrender.com
```

依次验证：

1. 打开这个网址 → 应该出现首页（第一次可能要等 30~60 秒，见下方说明）
2. 弹出密码框 → 输入 `13622`
3. 右上角齿轮 → 源管理 → 点播源 → 应该看到 **共 100 个**
4. 搜个「海贼王」→ 能出结果
5. 后台：网址后面加 `/admin` → 账号 `1362280` / 密码 `Aa230702`

---

## 第 7 步：分享给别人

把两样东西发过去就行：

```
网址：https://libretv-a1b2.onrender.com
密码：13622
```

对方手机、电脑打开都能用，**跟你电脑开不开机没关系**。

---

## 必须知道的 4 件事

### 1. 第一次打开慢是正常的
免费实例 **15 分钟没人访问会自动休眠**，下次有人打开要等 **30~60 秒**（页面转圈）。这是免费版的代价，不是坏了。

### 2. 内存只有 512MB
所以搜索并发我调到了 20。如果你觉得慢，可以在 Render 的 Environment 里把 `SEARCH_CONCURRENCY` 调到 `30` 试试（太高会崩）。

### 3. 访客统计会丢
后台的访问记录存在容器里，**每次重新部署会清零**。点播源不受影响（写在 `sources.json` 里）。

### 4. 构建失败怎么办
点开 **Logs**，往上翻找第一处红色 `error`，把最后 20 行截图发我。常见原因只有两种：
- `npm ci` 失败 → 仓库里少了 `package-lock.json`
- `next build` 内存不足 → 换 Node 运行方式重试（我给你改）

---

## 以后想改东西

改完本地文件 → GitHub Desktop 里会自动显示改动 → 左下角填一句说明 → 点 **Commit to main** → 右上角 **Push origin**。

Render 会自动重新部署（约 10 分钟），`autoDeploy: true` 已经在 `render.yaml` 里开好了。

**改密码**：Render 控制台 → 你的服务 → Environment → 改 `PASSWORD` → 保存，自动重启。

**加点播源**：改 `sources.json` 里的数组，push 上去即可。

---

## 可选：绑定自己的域名

1. 在 Render 服务页 → **Settings** → **Custom Domains** → **Add Custom Domain**
2. 输入你的域名（如 `tv.example.com`）
3. 去域名服务商那边加一条 CNAME，指向 Render 给你的地址
4. 等 DNS 生效（几分钟到几小时），Render 会自动签发 HTTPS 证书

> 域名需要先备案（如果用国内服务器）或者用境外注册商（不需要备案）。不确定的话这一步可以先跳过，用 `xxx.onrender.com` 完全够用。
