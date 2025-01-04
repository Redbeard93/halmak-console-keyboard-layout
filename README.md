# halmak-console-keyboard-layout
for arch linux
 In order to load the keymap at boot, specify the full path to the file in the KEYMAP variable in /etc/vconsole.conf. The file does not have to be gzipped as the official keymaps provided by kbd.
 
 then make hook change in /etc/minitcpio.conf,
 if you see something like "failed to start virtualconsole setup" while booting, make sure you change keymap hook in /etc/minitcpio.conf

 ```The custom keymap can be made persistent by setting it in /etc/vconsole.conf. Given this, if you use the sd-vconsole mkinitcpio hook instead of keymap, you could place your custom keymap file in /usr/share/kbd/keymaps/. This way its dependencies from /usr/share/kbd/keymaps will be automatically added to the initial ramdisk image by the hook. On the other hand, if you place your custom keymap file under /usr/local/, then its dependencies will need to be manually specified in the FILES array in mkinitcpio.conf```
 
 Here I just use `keymap` instead of `sd-vconsole` in `MODULE`, then add `personal.map` in path in `mkinitcpio.conf` `Files(/usr/local/share/kbd/keymaps/personal.map)`

 虽然只设置 /etc/vconsole.conf 可以在系统正常启动后实现持久化的键盘映射配置,但在早期启动阶段、系统恢复、故障排查以及系统更新等情况下,可能会因为初始内存盘中缺少自定义键盘映射文件而导致键盘映射不可用或不正确,从而影响系统的正常启动和操作.因此,为了确保在各种情况下都能正确使用自定义键盘映射,建议同时在 mkinitcpio.conf 的 FILES 数组中指定自定义键盘映射文件.
 

# 修改内核键盘映射的方法来修改keycode 
https://www.bilibili.com/read/cv5124452?spm_id_from=333.999.0.0


# Custom Keyboard Layouts with xkb

## 为什么需要自定义键盘布局？
- **开发者**：可能需要使用 `us intl` 键盘布局，因为这种布局的符号位置更符合编写代码的需要.
- **多语言写作者**：如果需要在多种语言（如英语、德语、意大利语）之间切换写作，而不想频繁切换不同的键盘布局，同时又能使用每种语言的特殊字符.

## xkb 配置系统
- **位置**：`xkb` 配置文件位于 `/usr/share/X11/xkb`，其中两个重要的子目录是：
  - `symbols`：包含键盘布局定义.
  - `rules`：提供将定义映射到配置的文件.
- **问题**：直接在这些目录中进行修改会导致在 `xkeyboard-config` 包升级时自定义更改被还原.

## xkb 用户配置
- **搜索路径**：`libxkbcommon` 库会按顺序搜索以下路径来定位 `xkb` 配置：
  1. `$XDG_CONFIG_HOME/xkb/` 或 `$HOME/.config/xkb/`（如果未定义 `$XDG_CONFIG_HOME`）
  2. `$HOME/.xkb/`
  3. `$XKB_CONFIG_EXTRA_PATH/xkb`（如果定义了 `$XKB_CONFIG_EXTRA_PATH`），否则是 `<sysconfdir>/xkb`（通常是 `/etc/xkb`）
  4. `$XKB_CONFIG_ROOT/X11/xkb`（如果定义了 `$XKB_CONFIG_ROOT`），否则是 `<datadir>/X11/xkb/`（通常是 `/usr/share/X11/xkb`）

## 创建自定义布局
- **基础布局**：以 `us` 布局的 `alt-intl` 变体为基础，因为它提供了大多数 US Intl 键盘的键，并且定义了德语变音符，但缺少 `ß`、段落符号和美分符号的键组合.
- **意大利语需求**：需要一个反引号键来输入带重音的意大利字母，因此将反引号设置为死键.
- **配置文件示例**：在 `~/.config/xkb/symbols` 中创建一个文件（例如 `codeaffen`），内容如下：
  ```xkb
  partial alphanumeric_keys
  xkb_symbols "usde" {
  
     include "us(alt-intl)"
  
     key <TLDE> { [ dead_grave, asciitilde, section,    plusminus    ] };
     key <AC02> { [          s,          S,  ssharp,    U1E9E        ] };
     key <AB03> { [          c,          C,    cent,    copyright    ] };
  
     include "level3(ralt_switch)"
  };
  ```
  ## 添加选项
- **创建 `evdev` 文件**：在 `~/.config/xkb/rules` 中创建一个 `evdev` 文件，内容如下：
  ```xkb
  ! option   = symbols
    codeaffen:usde  = +codeaffen(usde)

  ! include %S/evdev
  ```
  在 option = symbols 部分工作.

  
  定义一个选项，将 codeaffen:usde 映射到 symbols/codeaffen 中的 usde 部分.

  
  包含默认的 evdev 文件，否则可能会导致 Gnome 登录后会话崩溃.

  ## 使布局可发现
- **创建 `evdev.xml` 文件**：在 `~/.config/xkb/rules` 目录中创建一个 `evdev.xml` 文件，内容如下：
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE xkbConfigRegistry SYSTEM "xkb.dtd">
  <xkbConfigRegistry version="1.1">
    <layoutList>
      <layout>
        <configItem>
          <name>codeaffen</name>
          <shortDescription>usde</shortDescription>
          <description>English (US, codeaffen custom keymap)</description>
          <countryList>
            <iso3166Id>US</iso3166Id>
            <iso3166Id>DE</iso3166Id>
          </countryList>
          <languageList>
            <iso639Id>eng</iso639Id>
            <iso639Id>deu</iso639Id>
          </languageList>
        </configItem>
      </layout>
    </layoutList>
  </xkbConfigRegistry>
  ```
定义了一个名为 codeaffen 的新布局，映射到之前创建的定义文件.


定义了短描述 usde，在布局切换托盘图标中显示（如果配置了多个布局）.


定义了描述，在配置对话框中显示（例如在 Gnome 中的托盘选择器）.


将布局绑定到所有具有 ISO ID eng 和 deu 的语言，以及具有 ISO ID US 和 DE 的国家.


设置键盘


注意：可能需要注销并重新登录以使布局可用.


选择布局：现在可以在配置对话框中选择新定义的布局.

## 其他注意事项
- **Gnome**：要在 Gnome 中使用 `View Keyboard Layout` 选项查看所需的键盘布局图，需要将符号文件也放在系统目录中可用. 创建一个符号链接就足够了，并且是升级安全的：
  ```bash
  sudo ln -s ~/.config/xkb/symbols/codeaffen /usr/share/X11/xkb/symbols
  ```
  如果文件无法从系统路径访问，你将得到一个空窗口（例如 Fedora 38）或错误消息（例如 Ubuntu 22.04）.

- **localectl**：以这种方式配置的自定义布局无法通过 `localectl` 访问，因为该工具只打开 `/usr/share/X11/xkb/rules` 中的单个文件. 已在 `systemd` 项目中打开了一个错误报告.

[Custom Keyboard Layouts with xkb](https://codeaffen.org/2023/09/16/custom-keyboard-layouts-with-xkb/)
