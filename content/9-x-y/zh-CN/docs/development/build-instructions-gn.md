# 构建说明

请遵循以下指南来构建Electron。

## 平台要求

各个平台所对应的构建要求如下：

  * [macOS](build-instructions-macos.md#prerequisites)
  * [Linux](build-instructions-linux.md#prerequisites)
  * [Windows](build-instructions-windows.md#prerequisites)

## 前置知识

此外，你还需要安装[`depot_tools`][depot-tools]，这是一个用于获取Chromium，及其相关依赖工具。

另外，如果使用Windows系统, 你需要设置环境变量`DEPOT_TOOLS_WIN_TOOLCHAIN=0`。 依次打开 `Control Panel` → `System and
Security` → `System` → `Advanced system settings` ，然后添加系统变量 `DEPOT_TOOLS_WIN_TOOLCHAIN` ，并设置默认值为 `0`.  这将促使`depot_tools` 使用本地已安装的Visual Studio(默认状态下，`depot_tools`将会下载一个只有谷歌内部员工有权限使用的内部版本)。

## 缓存构建(可选步骤)

### GIT\_CACHE\_PATH

如果您计划多次构建 Electron，那么就添加 git 缓存以加快后续对`gclient`的调用。 要做到这一点，请设置一个 `GIT_CACHE_PATH` 环境变量：

```sh
$ export GIT_CACHE_PATH="${HOME}/.git_cache"
$ mkdir -p "${GIT_CACHE_PATH}"
# 这将使用大约16G
```

### 缓存

Thousands of files must be compiled to build Chromium and Electron. You can avoid much of the wait by reusing Electron CI's build output via [sccache](https://github.com/mozilla/sccache). This requires some optional steps (listed below) and these two environment variables:

```sh
export SCCACHE_BUCKET="electronjs-sccache-ci"
export SCCACHE_TWO_TIER=true
```

## 获取代码

```sh
$ mkdir electron-gn && cd electron-gn
$ gclient config --name "src/electron" --unmanaged https://github.com/electron/electron
$ gclient sync --with_branch_heads --with_tags
# This will take a while, go get a coffee.
```

> 除了使用 `https://github.com/electron/electron`， 你也可以使用你自己的 fork  (形如 `https://github.com/<username>/electron`)。

#### 拉/推的注意事项

如果您将来打算从 `electron` 官方地址进行 `git pull` 或 `git push`，那么您需要更新相应文件夹的源 URL。

```sh
$ cd src/electron
$ git remote remove origin
$ git remote add origin https://github.com/electron/electron
$ git checkout master
$ git branch --set-upstream-to=origin/master
$ cd -
```

:memo: `gclient` 会检查 `src/electron` 目录下的 `DEPS` 文件，从中获取依赖信息 (就像 Chromium 或 Node.js 那样)。 运行 `gclient sync -f` 确保所有用来构建 Electron 的依赖都符合该文件的描述。

运行以下命令拉取源码：
```sh
$ cd src/electron
$ git pull
$ gclient sync -f
```

## 构建

```sh
$ cd src
$ export CHROMIUM_BUILDTOOLS_PATH=`pwd`/buildtools
# 下一行只有在使用 scache 构建时才需要
$ exporter GN_EXTRA_ARGS="${GN_EXTRA_ARGS} cc_wrapper=\"${PWD}/electron/external_binaries/sccache\""
$ gn gen out/testing --args="import(\"//electron/build/args/testing. n\") $GN_EXTRA_ARGS"
```

若在 Windows 上 (没有可选参数)：
```sh
$ cd src
$ set CHROMIUM_BUILDTOOLS_PATH=%cd%\buildtools
$ gn out/testing --args="import(\"//electron/build/args/testing.gn\")
```

This will generate a build directory `out/Testing` under `src/` with the testing build configuration. You can replace `Testing` with another name, but it should be a subdirectory of `out`. Also you shouldn't have to run `gn gen` again—if you want to change the build arguments, you can run `gn args out/Testing` to bring up an editor.

To see the list of available build configuration options, run `gn args
out/Testing --list`.

**For generating Testing build config of Electron:**

```sh
$ gn gen out/Testing --args="import(\"//electron/build/args/testing.gn\") $GN_EXTRA_ARGS"
```

**For generating Release (aka "non-component" or "static") build config of Electron:**

```sh
$ gn gen out/Release --args="import(\"//electron/build/args/release.gn\") $GN_EXTRA_ARGS"
```

**To build, run `ninja` with the `electron` target:** Nota Bene: This will also take a while and probably heat up your lap.

For the testing configuration:
```sh
$ ninja -C out/Testing electron
```

For the release configuration:
```sh
$ ninja -C out/Release electron
```

This will build all of what was previously 'libchromiumcontent' (i.e. the `content/` directory of `chromium` and its dependencies, incl. WebKit and V8), so it will take a while.

你可以使用[sccache][sccache]命令来提高后面的构建过程。 Add the GN arg `cc_wrapper = "sccache"` by running `gn args out/Testing` to bring up an editor and adding a line to the end of the file.

The built executable will be under `./out/Testing`:

```sh
$ ./out/Testing/Electron.app/Contents/MacOS/Electron
# or, on Windows
$ ./out/Testing/electron.exe
# or, on Linux
$ ./out/Testing/electron
```

### 打包

On linux, first strip the debugging and symbol information:
```sh
electron/script/strip-binaries.py -d out/Release
```

To package the electron build as a distributable zip file:
```sh
ninja -C out/Release electron:electron_dist_zip
```

### 交叉编译

To compile for a platform that isn't the same as the one you're building on, set the `target_cpu` and `target_os` GN arguments. For example, to compile an x86 target from an x64 host, specify `target_cpu = "x86"` in `gn args`.

```sh
$ gn gen out/Testing-x86 --args='... target_cpu = "x86"'
```

Not all combinations of source and target CPU/OS are supported by Chromium.

<table>
<tr><th>Host</th><th>目标</th><th>状态</th></tr>
<tr><td>Windows x64</td><td>Windows arm64</td><td>实验功能</td>
<tr><td>Windows x64</td><td>Windows x86</td><td>Automatically tested</td></tr>
<tr><td>Linux x64</td><td>Linux x86</td><td>Automatically tested</td></tr>
</table>

If you test other combinations and find them to work, please update this document :)

See the GN reference for allowable values of [`target_os`][target_os values] and [`target_cpu`][target_cpu values].

#### Windows on Arm (experimental)
To cross-compile for Windows on Arm, [follow Chromium's guide](https://chromium.googlesource.com/chromium/src/+/refs/heads/master/docs/windows_build_instructions.md#Visual-Studio) to get the necessary dependencies, SDK and libraries, then build with `ELECTRON_BUILDING_WOA=1` in your environment before running `gclient sync`.

```bat
set ELECTRON_BUILDING_WOA=1
gclient sync -f --with_branch_heads --with_tags
```

Or (if using PowerShell):
```powershell
$env:ELECTRON_BUILDING_WOA=1
gclient sync -f --with_branch_heads --with_tags
```

Next, run `gn gen` as above with `target_cpu="arm64"`.


## 测试

To run the tests, you'll first need to build the test modules against the same version of Node.js that was built as part of the build process. To generate build headers for the modules to compile against, run the following under `src/` directory.

```sh
$ ninja -C out/Testing third_party/electron_node:headers
```

You can now [run the tests](testing.md#unit-tests).

可以通过增加其它标记来调试程序，例如：

```sh
$ npm run test -- \
  --enable-logging -g 'BrowserWindow module'
```

## Sharing the git cache between multiple machines

It is possible to share the gclient git cache with other machines by exporting it as SMB share on linux, but only one process/machine can be using the cache at a time. The locks created by git-cache script will try to prevent this, but it may not work perfectly in a network.

On Windows, SMBv2 has a directory cache that will cause problems with the git cache script, so it is necessary to disable it by setting the registry key

```sh
HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Lanmanworkstation\Parameters\DirectoryCacheLifetime
```

to 0. More information: https://stackoverflow.com/a/9935126

This can be set quickly in powershell (ran as administrator):

```powershell
New-ItemProperty -Path "HKLM:\System\CurrentControlSet\Services\Lanmanworkstation\Parameters" -Name DirectoryCacheLifetime -Value 0 -PropertyType DWORD -Force
```

## 故障排查

### Stale locks in the git cache
If `gclient sync` is interrupted while using the git cache, it will leave the cache locked. 如需移除锁，请将`--ignore_locks`参数传递至 `gclient sync`。

### I'm being asked for a username/password for chromium-internal.googlesource.com
If you see a prompt for `Username for 'https://chrome-internal.googlesource.com':` when running `gclient sync` on Windows, it's probably because the `DEPOT_TOOLS_WIN_TOOLCHAIN` environment variable is not set to 0. Open `Control Panel` → `System and Security` → `System` → `Advanced system settings` and add a system variable `DEPOT_TOOLS_WIN_TOOLCHAIN` with value `0`.  这将促使`depot_tools` 使用本地已安装的Visual Studio(默认状态下，`depot_tools`将会下载一个只有谷歌内部员工有权限使用的内部版本)。

[depot-tools]: http://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up

[sccache]: https://github.com/mozilla/sccache

[target_os values]: https://gn.googlesource.com/gn/+/master/docs/reference.md#built_in-predefined-variables-target_os_the-desired-operating-system-for-the-build-possible-values
[target_cpu values]: https://gn.googlesource.com/gn/+/master/docs/reference.md#built_in-predefined-variables-target_cpu_the-desired-cpu-architecture-for-the-build-possible-values
