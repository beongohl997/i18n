# ShortcutDetails 对象

* ` target `字符串-该快捷方式启动的目标。
* `cwd` String (optional) - The working directory. 默认值为空。
* `args` String (optional) - The arguments to be applied to `target` when launching from this shortcut. 默认值为空。
* `description` String (optional) - The description of the shortcut. Default is empty.
* `icon` String (optional) - The path to the icon, can be a DLL or EXE. `icon` and `iconIndex` have to be set together. Default is empty, which uses the target's icon.
* `iconIndex` Number (optional) - The resource ID of icon when `icon` is a DLL or EXE. Default is 0.
* `appUserModelId` String (optional) - The Application User Model ID. Default is empty.
