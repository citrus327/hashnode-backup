---
title: "使用changesets进行npm包发布"
datePublished: Wed Mar 22 2023 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqak2dmg000j08jrh1tuf6dz
slug: changesetsnpm
tags: changesets

---

## 介绍

目前在github上发npm包仍靠手动，需要一套较为稳定，可靠（背后有逻辑支持）的发布CI。保证git tag，version, branch, npm publish和github releases的执行。

相关文档：

* Semantic Versioning: 版本号规范
    

[Semantic Versioning 2.0.0](https://semver.org/)

* Changesets: [https://github.com/changesets/changesets](https://semver.org/)
    
* 集成结果可参考：[https://github.com/citrus327/react-lib-starter](https://semver.org/)
    

## 基础使用

1. 添加changesets工具链
    
    ```bash
    npm install @changesets/cli && npx changeset init
    ```
    
2. 创建一个changeset
    
    ```bash
    npx changeset
    ```
    
3. 修改版本
    
    ```bash
    npx changeset version
    ```
    
4. 发布
    
    ```bash
    npx changeset publish
    ```
    

## 详细解释

* 问题定义：
    
    当多人协作共同开发一个仓库时，会遇到来自各类的pr或者是同一分支的代码提交。这一系列需要被“发布”的信息单独从PR MR或者Commits信息上去看，略显单薄且无法很好的追溯。git本身也无法存储较为详细的信息。
    

changesets就是解决以上问题的工具。

在使用changesets时，需要将changeset理解为一次改动（intent to change）。一次改动所附带的信息有：

1. 版本号变更 (符合semver）
    
2. Changelog
    

使用`npx changeset`创建一个changeset后，会要求你输入一段本次变更的summary和期望变更的版本, 并在仓库的`.changesets` 文件夹内形成一个包含summary的`[UNIQUE_ID].md` 文件

例如：

```bash
|- .changeset
	|- chilly-ads-learn.md
|- package.json
```

`[UNIQUE_ID].md` 中会使用头部 frontmatter区域进行各个包的升级描述

```markdown
---
"package-a": patch
"package-b": patch
"package-c": patch
---

pump all packages to a patch version
```

所以当多个changesets被创建后，会出现多个`[UNIQUE_ID].md` 文件。changesets会根据这些文件的frontmatter信息核算出一个期望的版本，并再执行完`npx changeset version` 后变更版本，同时删除这些markdown文件。

## 发布

执行`npx changeset publish`或者手动执行`npm publish`即可。

鉴于很多repo基于`yarn`或者`pnpm` 的workspace protocol，建议使用对应包管理器的publish命令来更新子包相互依赖的版本号修正功能。

## Github action CI接入

1. 保证仓库设置中以下选项选择同意。
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1702882095850/5361d916-8b47-4918-b676-f455fb88ec7b.png align="center")
    
2. 保证NPM\_TOKEN设置完毕。
    
3. 新建workflow文件，参考：[https://github.com/citrus327/react-lib-starter/blob/main/.github/workflows/release.yml](https://semver.org/)
    
    以上workflow文件，使用pnpm作为包管理器，若使用yarn则自行修改。
    
4. 当每次有针对`main` 分支的提交时（且含有changeset），会触发该action，并根据提交的changesets进行分析，并自动提交PR。
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1702882112825/198a7dc7-5ba3-4039-8bb1-5b7af22f7608.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1702882115820/51556bde-f572-4715-a13e-8deb2832147c.png align="center")
    
    该PR会根据changeset信息自动修改npm包版本，[添加changeset信息至changelog.md](https://semver.org/)，且删除对应的changeset文件。
    
5. 同意PR之后，action会在再次工作，并检查相对应的changesets, 修改版本号并发布至对应registry。