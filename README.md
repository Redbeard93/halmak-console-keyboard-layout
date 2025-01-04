# halmak-console-keyboard-layout
for arch linux
 In order to load the keymap at boot, specify the full path to the file in the KEYMAP variable in /etc/vconsole.conf. The file does not have to be gzipped as the official keymaps provided by kbd.
 
 then make hook change in /etc/minitcpio.conf,
 if you see something like "failed to start virtualconsole setup" while booting, make sure you change keymap hook in /etc/minitcpio.console

 ```The custom keymap can be made persistent by setting it in /etc/vconsole.conf. Given this, if you use the sd-vconsole mkinitcpio hook instead of keymap, you could place your custom keymap file in /usr/share/kbd/keymaps/. This way its dependencies from /usr/share/kbd/keymaps will be automatically added to the initial ramdisk image by the hook. On the other hand, if you place your custom keymap file under /usr/local/, then its dependencies will need to be manually specified in the FILES array in mkinitcpio.conf```
 
 Here I just use `keymap` instead of `sd-vconsole` in `MODULE`, then add `personal.map` in path in `mkinitcpio.conf` `Files(/usr/local/share/kbd/keymaps/personal.map)`

 虽然只设置 /etc/vconsole.conf 可以在系统正常启动后实现持久化的键盘映射配置,但在早期启动阶段、系统恢复、故障排查以及系统更新等情况下,可能会因为初始内存盘中缺少自定义键盘映射文件而导致键盘映射不可用或不正确,从而影响系统的正常启动和操作.因此,为了确保在各种情况下都能正确使用自定义键盘映射,建议同时在 mkinitcpio.conf 的 FILES 数组中指定自定义键盘映射文件.
 

# 修改内核键盘映射的方法来修改keycode 
https://www.bilibili.com/read/cv5124452?spm_id_from=333.999.0.0
