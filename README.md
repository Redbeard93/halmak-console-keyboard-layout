# halmak-console-keyboard-layout
for arch linux
 In order to load the keymap at boot, specify the full path to the file in the KEYMAP variable in /etc/vconsole.conf. The file does not have to be gzipped as the official keymaps provided by kbd.
 
 then make hook change in /etc/minitcpio.conf,
 if you see something like "failed to start virtualconsole setup" while booting, make sure you change keymap hook in /etc/minitcpio.console

 ```The custom keymap can be made persistent by setting it in /etc/vconsole.conf. Given this, if you use the sd-vconsole mkinitcpio hook instead of keymap, you could place your custom keymap file in /usr/share/kbd/keymaps/. This way its dependencies from /usr/share/kbd/keymaps will be automatically added to the initial ramdisk image by the hook. On the other hand, if you place your custom keymap file under /usr/local/, then its dependencies will need to be manually specified in the FILES array in mkinitcpio.conf```
 Here I just use `keymap` instead of `sd-vconsole` in `MODULE`

# 修改内核键盘映射的方法来修改keycode 
https://www.bilibili.com/read/cv5124452?spm_id_from=333.999.0.0
