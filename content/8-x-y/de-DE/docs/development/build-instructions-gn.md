# Build Instructions

Follow the guidelines below for building Electron.

## Platform prerequisites

Check the build prerequisites for your platform before proceeding

  * [macOS](build-instructions-macos.md#prerequisites)
  * [Linux](build-instructions-linux.md#prerequisites)
  * [Windows](build-instructions-windows.md#prerequisites)

## GN prerequisites

You'll need to install [`depot_tools`][depot-tools], the toolset used for fetching Chromium and its dependencies.

Also, on Windows, you'll need to set the environment variable `DEPOT_TOOLS_WIN_TOOLCHAIN=0`. To do so, open `Control Panel` → `System and
Security` → `System` → `Advanced system settings` and add a system variable `DEPOT_TOOLS_WIN_TOOLCHAIN` with value `0`.  This tells `depot_tools` to use your locally installed version of Visual Studio (by default, `depot_tools` will try to download a Google-internal version that only Googlers have access to).

## Cached builds (optional step)

### GIT\_CACHE\_PATH

If you plan on building Electron more than once, adding a git cache will speed up subsequent calls to `gclient`. To do this, set a `GIT_CACHE_PATH` environment variable:

```sh
$ export GIT_CACHE_PATH="${HOME}/.git_cache"
$ mkdir -p "${GIT_CACHE_PATH}"
# This will use about 16G.
```

### sccache

Thousands of files must be compiled to build Chromium and Electron. You can avoid much of the wait by reusing Electron CI's build output via [sccache](https://github.com/mozilla/sccache). This requires some optional steps (listed below) and these two environment variables:

```sh
export SCCACHE_BUCKET="electronjs-sccache-ci"
export SCCACHE_TWO_TIER=true
```

## Getting the code

```sh
$ mkdir electron-gn && cd electron-gn
$ gclient config --name "src/electron" --unmanaged https://github.com/electron/electron
$ gclient sync --with_branch_heads --with_tags
# This will take a while, go get a coffee.
```

> Instead of `https://github.com/electron/electron`, you can use your own fork here (something like `https://github.com/<username>/electron`).

#### A note on pulling/pushing

If you intend to `git pull` or `git push` from the official `electron` repository in the future, you now need to update the respective folder's origin URLs.

```sh
$ cd src/electron
$ git remote remove origin
$ git remote add origin https://github.com/electron/electron
$ git checkout master
$ git branch --set-upstream-to=origin/master
$ cd -
```

:memo: `gclient` works by checking a file called `DEPS` inside the `src/electron` folder for dependencies (like Chromium or Node.js). Running `gclient sync -f` ensures that all dependencies required to build Electron match that file.

So, in order to pull, you'd run the following commands:
```sh
$ cd src/electron
$ git pull
$ gclient sync -f
```

## Building

```sh
$ cd src
$ export CHROMIUM_BUILDTOOLS_PATH=`pwd`/buildtools
# this next line is needed only if building with sccache
$ export GN_EXTRA_ARGS="${GN_EXTRA_ARGS} cc_wrapper=\"${PWD}/electron/external_binaries/sccache\""
$ gn gen out/Debug --args="import(\"//electron/build/args/debug.gn\") $GN_EXTRA_ARGS"
```

Or on Windows (without the optional argument):
```sh
$ cd src
$ set CHROMIUM_BUILDTOOLS_PATH=%cd%\buildtools
$ gn gen out/Debug --args="import(\"//electron/build/args/debug.gn\")"
```

This will generate a build directory `out/Debug` under `src/` with debug build configuration. You can replace `Debug` with another name, but it should be a subdirectory of `out`. Also you shouldn't have to run `gn gen` again—if you want to change the build arguments, you can run `gn args out/Debug` to bring up an editor.

To see the list of available build configuration options, run `gn args
out/Debug --list`.

**For generating Debug (aka "component" or "shared") build config of Electron:**

```sh
$ gn gen out/Debug --args="import(\"//electron/build/args/debug.gn\") $GN_EXTRA_ARGS"
```

**For generating Release (aka "non-component" or "static") build config of Electron:**

```sh
$ gn gen out/Release --args="import(\"//electron/build/args/release.gn\") $GN_EXTRA_ARGS"
```

**To build, run `ninja` with the `electron` target:** Nota Bene: This will also take a while and probably heat up your lap.

For the debug configuration:
```sh
$ ninja -C out/Debug electron
```

For the release configuration:
```sh
$ ninja -C out/Release electron
```

This will build all of what was previously 'libchromiumcontent' (i.e. the `content/` directory of `chromium` and its dependencies, incl. WebKit and V8), so it will take a while.

To speed up subsequent builds, you can use [sccache][sccache]. Add the GN arg `cc_wrapper = "sccache"` by running `gn args out/Debug` to bring up an editor and adding a line to the end of the file.

The built executable will be under `./out/Debug`:

```sh
$ ./out/Debug/Electron.app/Contents/MacOS/Electron
# or, on Windows
$ ./out/Debug/electron.exe
# or, on Linux
$ ./out/Debug/electron
```

### Pakete erstellen

On linux, first strip the debugging and symbol information:
```sh
electron/script/strip-binaries.py -d out/Release
```

To package the electron build as a distributable zip file:
```sh
ninja -C out/Release electron:electron_dist_zip
```

### Cross-compiling

To compile for a platform that isn't the same as the one you're building on, set the `target_cpu` and `target_os` GN arguments. For example, to compile an x86 target from an x64 host, specify `target_cpu = "x86"` in `gn args`.

```sh
$ gn gen out/Debug-x86 --args='... target_cpu = "x86"'
```

Nicht alle Kombinationen von Quell- und Ziel-Prozessor/Betriebssystem werden von Chromium unterstützt.

<table>
<tr><th>Host</th><th>Ziel</th><th>Status</th></tr>
<tr><td>Windows x64</td><td>Windows arm64</td><td>Experimentell</td>
<tr><td>Windows x64</td><td>Windows x86</td><td>Automatisch getestet</td></tr>
<tr><td>Linux x64</td><td>Linux x86</td><td>Automatisch getestet</td></tr>
</table>

Wenn Sie andere Kombinationen testen und diese funktionieren aktualisieren Sie bitte dieses Dokument :)

See the GN reference for allowable values of [`target_os`][target_os values] and [`target_cpu`][target_cpu values].

#### Windows auf Arm (experimentell)
Um Windows auf Arm zu kompilieren, folgen Sie[dem Chromium-Guide](https://chromium.googlesource.com/chromium/src/+/refs/heads/master/docs/windows_build_instructions.md#Visual-Studio) um die notwendigen Abhängigkeiten, SDK und Bibliotheken zu erhalten, und bauen Sie dann mit `ELECTRON_BUILDING_WOA=1` in Ihrer Umgebung, bevor Sie `gclient sync`ausführen.

```bat
set ELECTRON_BUILDING_WOA=1
gclient sync -f --with_branch_heads --with_tags
```

Oder (wenn Sie PowerShell benutzen):
```powershell
$env:ELECTRON_BUILDING_WOA=1
gclient sync -f --with_branch_heads --with_tags
```

Als nächstes führe `gn gen` aus wie oben mit `target_cpu="arm64"`.


## Tests

Um die Tests durchzuführen, müssen Sie zuerst die Testmodule gegen die gleiche Version von Node.js bauen, die als Teil des Build-Prozesses gebaut wurde. Um Build Header für die Module zu kompilieren, führen Sie folgende Befehle in dem `src/` Verzeichnis aus.

```sh
$ ninja -C out/Debug third_party/electron_node:headers
```

You can now [run the tests](testing.md#unit-tests).

Wenn Sie etwas debuggen kann es hilfreich sein einige zusätzlichen Flaggen zu der Electron Binärdatei zu übergeben:

```sh
$ ./out/Debug/Electron.app/Contents/MacOS/Electron electron/spec \
  --ci --enable-logging -g 'BrowserWindow module'
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

## Problemlösungen

### Stale locks in the git cache
If `gclient sync` is interrupted while using the git cache, it will leave the cache locked. To remove the lock, pass the `--break_repo_locks` argument to `gclient sync`.

### I'm being asked for a username/password for chromium-internal.googlesource.com
If you see a prompt for `Username for 'https://chrome-internal.googlesource.com':` when running `gclient sync` on Windows, it's probably because the `DEPOT_TOOLS_WIN_TOOLCHAIN` environment variable is not set to 0. Open `Control Panel` → `System and Security` → `System` → `Advanced system settings` and add a system variable `DEPOT_TOOLS_WIN_TOOLCHAIN` with value `0`.  This tells `depot_tools` to use your locally installed version of Visual Studio (by default, `depot_tools` will try to download a Google-internal version that only Googlers have access to).

[depot-tools]: http://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up

[sccache]: https://github.com/mozilla/sccache

[target_os values]: https://gn.googlesource.com/gn/+/master/docs/reference.md#built_in-predefined-variables-target_os_the-desired-operating-system-for-the-build-possible-values
[target_cpu values]: https://gn.googlesource.com/gn/+/master/docs/reference.md#built_in-predefined-variables-target_cpu_the-desired-cpu-architecture-for-the-build-possible-values
