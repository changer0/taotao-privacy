# privacy-site —— 线上隐私政策站点（git subtree）

本目录是「桃桃记账」线上隐私政策页面，通过 **git subtree** 纳入主工程一起维护。

| 项 | 值 |
|---|---|
| 上游仓库 | [`changer0/taotao-privacy`](https://github.com/changer0/taotao-privacy)（GitHub Pages） |
| subtree 前缀 | `privacy-site/`（相对仓库根目录） |
| 远程别名 | `privacy-origin` → `https://github.com/changer0/taotao-privacy.git` |
| 上游分支 | `main` |
| 线上地址 | <https://privacy.zhanglulu.com.cn/> |
| 域名解析 | `zhanglulu.com.cn` 子域 `privacy` → CNAME → `changer0.github.io`（海外解析，免备案） |

> **什么是 subtree**：上游仓库的内容被嵌进主工程的 `privacy-site/` 子目录，既能跟主工程一起提交，也能用 `git subtree push / pull` 跟上游仓库双向同步。
>
> ⚠️ 所有 `git subtree` 命令都要在**仓库根目录**执行，不要进到 `privacy-site/` 里跑。

---

## 0. 首次准备（每台机器 / 每次新 clone 只做一次）

`privacy-origin` 这个远程别名存在本地 `.git/config` 里，**不随仓库提交**。新 clone 下来的仓库没有它，先加上：

```bash
# 在仓库根目录执行
git remote add privacy-origin https://github.com/changer0/taotao-privacy.git
```

检查是否已配置（有输出即已配好，不用重复加）：

```bash
git remote -v | grep privacy-origin
```

---

## 1. 改内容

只改 `privacy-site/index.html`。改的时候必须保持三处一致（**合规要求，缺一不可**）：

| 改这里 | 是什么 |
|---|---|
| `lib/src/features/privacy/domain/privacy_content.dart` 的 `PrivacyPolicyText` | App 内本地隐私页（正文事实来源） |
| `privacy-site/index.html` | 线上同款文案 + 运营主体 / 邮箱 / 生效日期 / 版本号 |
| `lib/src/features/privacy/domain/privacy_consent.dart` 的 `kPrivacyPolicyVersion` | 隐私同意版本号（有实质变更才升） |

有实质变更时，记得同时改 `index.html` 里页面**顶部和底部的版本号 / 生效日期**。

> 完整三处同步规则见 `docs/ai/README.md`「隐私政策（线上）维护」。

---

## 2. 提交（先 commit 才能发布）

`git subtree push` 发布的是**已提交的历史**，不是工作区里没存盘的改动。所以改完先提交到主工程：

```bash
git add privacy-site/index.html        # 连带其它同步改的文件一起 add
git commit -m "docs(privacy): 更新隐私政策到 vXXXX.XX.XX"
```

走 PR 合并到 `main`，或直接提交到 `main` 都行——发布时以 `main` 上的内容为准。

---

## 3. 发布上线 ⭐（你最常用的一步）

确认要发布的内容已经在 `main` 上（PR 已合并 / 已直接提交），然后：

```bash
# 1) 确保本地 main 是最新的
git checkout main
git pull

# 2) 在仓库根目录推送 privacy-site 到上游仓库，触发 GitHub Pages 重新部署
git subtree push --prefix=privacy-site privacy-origin main
```

这条 `subtree push` 做的事：

1. 把 `privacy-site/` 的历史单独抽出来；
2. 推送到上游仓库 `changer0/taotao-privacy` 的 `main` 分支；
3. GitHub Pages 自动重新部署，**约 1 分钟后** <https://privacy.zhanglulu.com.cn/> 生效。

> 第一次 push 会遍历较多历史、稍慢，属正常。push 需要对 `changer0/taotao-privacy` 有写权限。

---

## 4. 验证是否生效

```bash
# 看上游仓库 main 的最新提交是否已更新
git ls-remote privacy-origin main

# 看上游仓库 main 上的 index.html 版本号
git fetch privacy-origin main && git show privacy-origin/main:index.html | grep 政策版本
```

线上页面带 CDN 缓存，约 1 分钟后**强制刷新**浏览器查看：<https://privacy.zhanglulu.com.cn/>

---

## 5. 拉取上游改动（很少用）

正常都以本工程为准、单向往外推。**只有**当有人直接在 GitHub 网页上改了上游仓库、需要把改动同步回本工程时才用：

```bash
git subtree pull --prefix=privacy-site privacy-origin main --squash
```

（`--squash` 要和当初 `subtree add --squash` 保持一致。）

---

## 目录文件

| 文件 | 作用 | 能不能动 |
|---|---|---|
| `index.html` | 隐私政策页面本体 | 改这里，必须与 App 内文案同步 |
| `CNAME` | GitHub Pages 自定义域名绑定 | **不要删**，删了自定义域名失效；换域名才改 |
| `e01d7921….txt` | 域名 / 网站所有权校验文件 | **不要删 / 改**，删了线上校验会失效 |
| `README.md` / `.gitignore` | 本说明 / 忽略规则 | 工程内维护用 |

> `subtree push` 会把整个 `privacy-site/` 目录（含本 README、`.gitignore`）一并推到上游公开仓库，但 GitHub Pages 只对外展示 `index.html`，其余文件不影响线上页面。

---

## 速查

```bash
# 一次性：配置 remote（新机器/新 clone）
git remote add privacy-origin https://github.com/changer0/taotao-privacy.git

# 日常：改完 index.html、提交并进 main 后，一行发布
git subtree push --prefix=privacy-site privacy-origin main
```

> App 内「在线隐私政策」入口读 `lib/src/features/privacy/domain/privacy_links.dart` 的 `onlinePolicyUrl`，换地址只改这一处常量。
