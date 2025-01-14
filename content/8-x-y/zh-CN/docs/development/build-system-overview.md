# 构建系统概述

Electron 使用 [GN](https://gn.googlesource.com/gn) 生成项目，并用 [Ninja](https://ninja-build.org/) 完成构建。 项目配置位于 `.gn` 和 `.gni` 文件中。

## GN 文件

The following `gn` files contain the main rules for building Electron:

* `BUILD.gn` defines how Electron itself is built and includes the default configurations for linking with Chromium.
* `build/args/{debug,release,all}.gn` contain the default build arguments for building Electron.

## 分块构建

由于 Chromium 项目及其庞大，最终的链接阶段往往需要数分钟，加大了开发难度。 为此，Chromium 采用了“分块构建”方式，将每个模块作为单独的动态库构建，虽然影响了文件大小和性能，但加快了链接速度，

Electron 也继承了 Chromium 这一构建方式。 在 `Debug` 模式下构建时，程序将与 Chromium 的动态库链接，以加快链接速度；而 `Release` 模式下程序则会与静态库链接，以优化程序大小和性能。

## 测试

**注意：**_此内容已经过时，部分信息对于使用 GN 构建的 Electron 已不再适用。</p>

使用以下方式测试你的修改符合项目编码风格：

```sh
$ npm run lint
```

测试功能使用：

```sh
$ npm test
```

每当您更改Electron源代码时，都需要在测试之前重新运行构建：

```sh
$ npm run build && npm test
```

你可以通过分离特定的测试来让测试套件运行速度更快或阻止你目前正在使用摩卡的 [独占性测试](https://mochajs.org/#exclusive-tests)功能. 追加 `.only` 到任何 `describe` 或 `it` 函数调用：

```js
describe.only('some feature', function () {
  // ... only tests in this block will be run
})
```

或者，您可以使用 mocha 的 `grep` 选项来只运行与给定正则表达式模式匹配的测试

```sh
$ npm test -- --grep child_process
```

包含本地模块(例如`runas`)的测试无法在调试版本中执行 (查看 [#2558](https://github.com/electron/electron/issues/2558) 获取详情), 但它们将与发行版本一起使用。

要使用发行构建来运行测试：

```sh
$ npm test -- -R
```
