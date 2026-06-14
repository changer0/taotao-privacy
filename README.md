# privacy-site —— 线上隐私政策（GitHub Pages 部署仓库）

本目录是「桃桃记账」线上隐私政策站点，通过 **git subtree** 纳入主工程一起维护，对应独立部署仓库 [`changer0/taotao-privacy`](https://github.com/changer0/taotao-privacy)。

- 线上地址：<https://privacy.zhanglulu.com.cn/>
- 托管：GitHub Pages 仓库 `changer0/taotao-privacy`（根目录放 `index.html`）
- 域名：`zhanglulu.com.cn` 子域 `privacy` → CNAME → `changer0.github.io`（海外解析，免备案）

## 目录文件

| 文件 | 作用 | 能不能动 |
|---|---|---|
| `index.html` | 隐私政策页面本体 | 改这里，必须与 App 内文案同步（见下） |
| `CNAME` | GitHub Pages 自定义域名绑定 | **不要删**；换域名才改 |
| `e01d7921…​.txt` | 域名 / 网站所有权校验文件 | **不要删 / 改**，删了线上校验会失效 |

## 内容必须与应用内一致（合规要求）

正文事实来源是 `lib/src/features/privacy/domain/privacy_content.dart` 的 `PrivacyPolicyText`。
本目录 `index.html` 是它的同款文案 + 运营主体 / 联系邮箱 / 生效日期 / 版本号。**改任意一处都要同步另一处。**

## 发布到线上（git subtree）

本目录是 `changer0/taotao-privacy` 的 subtree。改完 `index.html` 后，从**仓库根目录**执行：

```bash
# 推送本目录到 GitHub Pages 仓库，约 1 分钟后线上生效
git subtree push --prefix=privacy-site privacy-origin main
```

> remote `privacy-origin` = `https://github.com/changer0/taotao-privacy.git`
> 若未配置：`git remote add privacy-origin https://github.com/changer0/taotao-privacy.git`

拉取线上改动（一般不需要，因为以本工程为准）：

```bash
git subtree pull --prefix=privacy-site privacy-origin main --squash
```

## 更新隐私政策的完整步骤

1. 改 `privacy_content.dart` 的 `PrivacyPolicyText`（App 内本地页）。
2. 同步改本目录 `index.html`；有实质变更时升 `kPrivacyPolicyVersion`（`privacy_consent.dart`）以及页面里的版本号 / 生效日期。
3. `git subtree push --prefix=privacy-site privacy-origin main` 发布。

## App 内链接

应用内「在线隐私政策」入口读 `lib/src/features/privacy/domain/privacy_links.dart` 的 `onlinePolicyUrl`，换地址只改这一处常量。

> 完整背景与决策见 `docs/ai/README.md`「隐私政策（线上）维护」一节。
