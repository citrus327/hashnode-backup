---
title: "Semantic Versioning"
datePublished: Tue Apr 21 2020 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqajfcxh000208jrgpoi8n8e
slug: semantic-versioning
tags: npm

---

## 版本号说明

* c: patch number，一些 bug fix
    
* b: minor number, 新增 feature, 且向后兼容
    
* a: major number, 大型改动，可能会有 break change，且可能不会向后兼容
    
* 有-rc -beta -alpha 后缀，只有指定特定版本才会被版本号命中
    

| Code status | Stage | Rule | Example version |
| --- | --- | --- | --- |
| First release | New product | Start with 1.0.0 | 1.0.0 |
| Backward compatible bug fixes | Patch release | Increment the third digit | 1.0.1 |
| Backward compatible new features | Minor release | Increment the middle digit and reset last digit to zero | 1.1.0 |
| Changes that break backward compatibility | Major release | Increment the first digit and reset middle and last digits to zero | 2.0.0 |

## 尝试版本范围

[semver.npmjs.com](semver.npmjs.com)

## 版本范围定义

* ^3.3.0：代表接受 3.3.0 以上任何 minor version 的版本
    
    * 例如 3.4.0, 3.4.1, 3.9.1
        
    * 反例：3.2.1 和 4.0.0 不在范围内
        
* ~3.3.0: 代表接受 3.3.0 以上任何 patch version 的版本
    
    * 例如 3.3.0, 3.3.1, 3.3.2
        
    * 反例：3.4.1, 3.5.1, 4.0.0 不在范围内
        
* 如果不想指定最低版本，可以使用 3.3.x 或者 3.x 来代表任意的 patch 和 minor version
    

## 版本升级

* 升级 patch: `npm version patch`
    
* 升级 verson: `npm version minor`
    
* 升级 major: `npm version major`
    

## 一些例子

* 例如安装 jquery， `npm install jquery@~3.3.0`，我想要安装的是 3.3.0 以上最高的 patch version 但是在 package.json 中会发现，dependencies 里会有 `jquery: ^3.3.1`，这是因为 npm semver 默认你接受 3.3.1 以上的所有 minor version。那以后在重新安装依赖的时候 (`npm install`)，会根据 package.json 里的版本号来判定。这可能不是你所期望的，可以通过手动指定 package.json 里的版本号来修改这个默认行为。
    

## Reference

[https://semver.npmjs.com/](semver.npmjs.com) [https://docs.npmjs.com/about-semantic-versioning](semver.npmjs.com) [https://www.youtube.com/watch?v=kK4Meix58R4](semver.npmjs.com) [https://www.youtube.com/watch?v=mpkC6MmKgsQ](semver.npmjs.com)