# 自定义 Linux 桌面启动器行为

在很多的 Linux 环境中，你可以通过修改文件 `.desktop` 来在它的启动器中添加自定义条目。 关于Canonical 的 Unity 启动器文档, 参考 [Adding Shortcuts to a Launcher][unity-launcher]. 可以通过 [freedesktop.org Specification][spec]网站，找到更详细的参考信息。

__Audacious 的启动器快捷方式:__

![audacious][3]

一般情况下，在快捷键menu中为每一个条目添加`Name` 和`Exec`属性就可以将其设置为快捷键。 用户点击快捷见时，Unity启动器就会执行`Exec` 属性。 其形式如下：

```plaintext
Actions=PlayPause;Next;Previous

[Desktop Action PlayPause]
Name=Play-Pause
Exec=audacious -t


[Desktop Action Next]
Name=Next
Exec=audacious -f


[Desktop Action Previous]
Name=Previous
Exec=audacious -r
```

Unity启动器通常通过参数的形式来操作应用。 你在应用的全局变量`process.argv`中发现这一规律。

[3]: https://help.ubuntu.com/community/UnityLaunchersAndDesktopFiles?action=AttachFile&do=get&target=shortcuts.png

[unity-launcher]: https://help.ubuntu.com/community/UnityLaunchersAndDesktopFiles#Adding_shortcuts_to_a_launcher
[spec]: https://specifications.freedesktop.org/desktop-entry-spec/1.1/ar01s11.html
