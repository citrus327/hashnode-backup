---
title: "@rollup/node-resolve"
datePublished: Wed Mar 20 2024 06:08:00 GMT+0000 (Coordinated Universal Time)
cuid: cltzejmjc001f0ale3utya04g
slug: rollupnode-resolve-1

---


## 基础信息

- order: `post`
- cache实现：[https://github.com/rollup/plugins/blob/master/packages/node-resolve/src/cache.js](https://github.com/rollup/plugins/blob/master/packages/node-resolve/src/cache.js)
- `resolveSymlink`处理：[https://github.com/rollup/plugins/blob/master/packages/node-resolve/src/fs.js](https://github.com/rollup/plugins/blob/master/packages/node-resolve/src/fs.js)

## 主要流程

<details>
<summary>`resolveId`</summary>
- `resolveLikeNode` 为主要处理方法，以下为流程
	- 看要不要读browser字段
	- 看是不是scoped package, 或者为相对路径
	- 如果不是相对路径，且没有标明在resolveOnly里。则看是否为entry文件，若在，则交给其他插件处理，否则当作external
	- → `resolveImportSpecifiers` 见下文
	- 拿到结果后看看结果是否为builtin module, 这里会检测preferBuiltins选项[https://github.com/rollup/plugins/tree/master/packages/node-resolve#preferbuiltins](https://github.com/rollup/plugins/tree/master/packages/node-resolve#preferbuiltins)
	- 如果结果为falsy，则defer
	- 这个结果的数据结构为：`packageInfo`, `hasModuleSideEffects`, `hasPackageEntry`, `packageBrowserField`, `location` location为具体位置，精确到文件位置
	- 检查rollup `preserveSymlinks` 配置，看是否要遵循symlink。同时得到精确的文件位置
	- 设置信息至`idToPackageInfo.set(location, packageInfo);`
	- 检查插件配置`jail` 看是否要包含在bundle内，若在jail外，则直接视为external
	- 最后返回

	```javascript
	{
	  id: `${location}${importSuffix}`,
	  moduleSideEffects: hasModuleSideEffects(location)
	};
	```

	- 例如：`@blizzbolts/typus`会解析成：`'/Users/hao/spaces/projj/github.com/citrus327/learning-rollup/node_modules/.pnpm/registry.npmmirror.com+@blizzbolts+typus@0.1.0/node_modules/@blizzbolts/typus/dist/index.mjs’`
	- 例如：`@blizzbolts/typus?a=1`会解析成：`'/Users/hao/spaces/projj/github.com/citrus327/learning-rollup/node_modules/.pnpm/registry.npmmirror.com+@blizzbolts+typus@0.1.0/node_modules/@blizzbolts/typus/dist/index.mjs?a=1’`
- `resolveImportSpecifiers`
	- 最终返回一个`id, moduleSideEffects`的对象，作为resolveId的结果，根据不同的package类型，使用以下2个方法branch
	- 如果包内有exports字段，使用`resolveWithExportMap`解析 → 下文
	- 如果没有exports字段，使用`resolveWithClassic`解析 → 下文
- `resolveWithExportMap`
	- 整体逻辑就是尝试使用包内的exports字段找到实际包的entry
	- 如果当前要导入的包以`#` 开头，则使用`resolvePackageImports`解析 → 下文
	- 否则查看当前包的packageName, 使用`getPackageName`方法，其实就是获取实际包名，例如`@scope/package/index.js`会解析成`@scope/package`, `package/index.js`会解析成`package`
		- 如果当前包名解析完成后以`.`或者`/` 开头，直接defer
	- 根据packageName寻找包的实际位置（`getPackageJson`），这里会读取一些插件的options，例如`moduleDirectory`
		- getPackageJson内部会调用`findPackageJson`方法，实际就是寻找当前文件下的package.json，如果找不到就使用上个文件夹的路径继续寻找，是一个while loop的递归
- `resolveWithClassic`
	- 循环importSpecifierList，调用`resolveIdClassic`即使用`resolve`解析 [https://github.com/browserify/resolve](https://github.com/browserify/resolve)
- `resolveLikeNode`后会将resolve的结果交给其他插件（使用`this.resolve(xxx)`）主动交给其他插件处理，xxx参数中会在options中插入额外的custom参数，内含被`node-resolve`插件解析的结果
	- [https://github.com/rollup/plugins/blob/master/packages/node-resolve/src/index.js#L302](https://github.com/rollup/plugins/blob/master/packages/node-resolve/src/index.js#L302)

</details>

