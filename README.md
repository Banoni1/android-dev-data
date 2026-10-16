# android-dev-data

[En（英语学习 App）](https://github.com/Banoni1/android-dev) 用到的离线数据。

**这个仓库只放 App 需要的公共数据，不放任何用户数据，也不放 App 的源码或构建产物。**

## 离线词典数据

App 的 RSS 阅读界面可以点词查义，查的是本地词典。词典包放在本仓库的 Release 里，
App 从固定地址下载：

```
https://github.com/Banoni1/android-dev-data/releases/latest/download/ecdict-zh-en.sqlite.gz
```

用 `releases/latest/download/` 这个形式，以后换词典版本只要发新 Release，App 那边
一个字都不用改。

### 资产说明

| 文件 | 词条数 | 解开后 | 下载 |
|---|---|---|---|
| `ecdict-zh-en.sqlite.gz` | 770,611 | 92.1 MB | 38.5 MB |

解开后是一个 SQLite 文件，三张表：

- `entries` —— 词条。`word`（原样词头）、`word_lower`（查询键）、`phonetic`（音标）、
  `translation`（中文释义）、`definition`（英文释义），另有 `pos` / `collins` /
  `oxford` / `tag` / `bnc` / `frq` 六个短字段备用（App 目前不显示，留着以后给
  文章标生词难度用）。
- `forms` —— 词形反查，`form_lower` → `base_lower`（`running` → `run`）。
- `package_info` —— 这本词典的身份证：名字、数据来源、许可、构建日期。

表结构的权威定义在 App 仓库的 `Preposition App/preposition_app/tools/build_dict_package.py`
（生成脚本）。改表结构要两边一起改。

### 数据来源与许可

数据来自 [ECDICT](https://github.com/skywind3000/ECDICT)，**MIT 许可**，可自由分发。

一个数据特性需要说明：ECDICT 的**英文释义只覆盖约 21% 的词条**（有词频标记的常用词
里覆盖 92.8%，冷词里只有 16.7%），而且个别变形词的英文释义会串到同形异义词上
（比如 `ate` 的英文释义是希腊女神 Ate 的词条）。这是上游数据的特性，不是打包出错。

## 重新生成词典包

见 App 仓库的 `tools/build_dict_package.py` 头部说明。大致是：下载 ECDICT 的
`ecdict.csv`，跑脚本生成 `.sqlite`，`gzip` 后作为附件发一个新 Release。
