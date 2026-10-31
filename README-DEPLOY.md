# 部署到 Render（免费，不依赖你的电脑）

这个文件夹已经是「可以直接上 Render」的完整项目：100 个点播源、看片密码、管理后台、首页样式全都在里面。

- 看片密码：`13622`
- 管理后台：`/admin`，账号 `1362280`，密码 `Aa230702`

---

## 第 1 步：把它放到 GitHub

Render 只能从 Git 仓库拉代码，所以先上传到 GitHub。

**推荐：用 GitHub Desktop（最省事）**

1. 装 [GitHub Desktop](https://desktop.github.com/)，用 GitHub 账号登录（没有就注册，邮箱即可）
2. 菜单 `File → Add local repository` → 选这个文件夹 `LibreTV-render`
   - 提示「不是 Git 仓库」时点 `create a repository`
3. 右上角点 **Publish repository**
   - 名字填 `libretv`
   - **取消勾选 "Keep this code private"**（Render 免费版要读公开仓库）
4. 发布完成，浏览器里能看到仓库页面即可

**备用：网页上传（没有 GitHub Desktop 时）**

GitHub 网页单次最多传 100 个文件，所以分 4 批拖：

1. 新建仓库 `libretv`（Public）→ `Add file → Upload files`
2. 依次拖入（每次点一次 Commit）：
   - `src/lib`（50 个文件）
   - `src/components`（34 个）
   - `src/app`（31 个）
   - `public`（10 个）
   - 根目录的 15 个文件（Dockerfile、package.json、start.js、sources.json 等）
3. 注意根目录文件要拖到仓库根，不要拖进子文件夹

---

## 第 2 步：在 Render 里建服务

1. 打开 <https://dashboard.render.com/>，用 **GitHub 账号登录**（不用绑卡）
2. 右上 `New +` → **Blueprint**
3. 选刚才的 `libretv` 仓库 → Render 会自动读到仓库里的 `render.yaml`
4. 点 **Apply** / **Create**
5. 等构建（首次约 8~15 分钟，会看到 Logs 滚动）；状态变成 **Live** 就成功了

> 如果 Blueprint 提示不支持，改用：`New +` → **Web Service** → 选仓库 → Runtime 选 **Docker** → Plan 选 **Free** → Region 选 **Singapore** → Create。环境变量在 `Environment` 里按 `render.yaml` 里那几项手动加一遍即可。

---

## 第 3 步：拿到地址

服务页顶部会显示形如：

```
https://libretv-xxxx.onrender.com
```

- 打开就是首页，输密码 `13622`
- 后台：`https://libretv-xxxx.onrender.com/admin`

---

## 必须知道的免费版限制

| 项目 | 说明 |
|---|---|
| **冷启动** | 15 分钟没人访问会自动休眠，下一次打开要等 **30~60 秒**（页面转圈，不是坏了） |
| **内存** | 512MB，所以搜索并发已调到 20，比本地慢一点 |
| **数据** | 访客统计存在容器里，**重新部署会清零**（点播源不受影响，它们写在 sources.json） |
| **带宽** | 每月 100GB 出站流量，几个人看片够用 |
| **构建失败** | 极少见；如果卡在 `next build` 内存不足，把 `render.yaml` 里 `plan: free` 保持不变，改用 Node 运行时重新建一次即可 |

---

## 想换配置

改仓库里的文件再提交，Render 会自动重新部署：

- 加/删点播源 → 改 `sources.json`
- 改密码 → 改 `render.yaml` 里的 `PASSWORD`（或在 Render 面板 Environment 里改）
- 首页文案/样式 → 改 `src/components/home-sections.tsx`
