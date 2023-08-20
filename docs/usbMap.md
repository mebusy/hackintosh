

# USB 定制

1. 进入windos, 运行 usbtoolbox ( option: D/S/C )
    - 输入D (Discover Ports)
        - 用usb3 u盘插遍所有usb端口
        - 再用 usb2.0 u盘把 所有识别的 usb3 接口再插一遍
        - 有 type-C 接口，正反都插一下 ?
        - B 返回主菜单
    - 输入C (Change Settings)
        - 再次输入 C (Bind Companions) 关闭这个选项, 
            - 主要的目的是，等一下如果端口数超过15个，可以单独关闭一个端口，而不会影响到其他关联的端口。
        - B 返回主菜单
    - 输入S (Select Ports and Build Kext)  开始编辑端口映射数据
        - 这时候需要观察一下，现在是不是你所有的物理接口都识别成功了。
        - 注意 XHCI 那里的数字 e.g. (18/21) , 如果超过了15， 那么我们就需要关闭几个端口 来确保macos 可以识别到，因为macos 有这个限制。
            - 优先关闭 扫描工具错误添加的端口
            - 再 关闭 USB3 物理端口上的 USB2.0 端口关闭。
            - 确保端口总数 在 15个以下。
        - 完成之后，输入K (Build UTBMap.kext) 创建端口映射数据。
            - 放到U盘中 备用


2. 重启进入 MacOS, 打开 hackintool
    - USB面板, 先 清理 USB 数据, 再 刷新一下
    - 再点击 注入, 最后导出数据
        - 只需要USBPorts.kext 文件

3. ProperTree 分别打开 USBPorts.kext 和 UTBMap.kert 的 info.plist 文件
    - 把 UTBMap.kert 端口映射的数据，复制到 USBPorts.kext
    - USBPorts info.plist `/Root/IOKitPersonalities/`
        - 列出的控制器中， 只保留 UTBMap.kert  中有的，删除其他的
    - 然后分别 各自打开 控制器子项,
        - 把 UTBMap.kert  控制器子项的 IOProviderMergeProperties 下的 port-count 和 ports 覆盖到 USBPorts 子项中

4. 把USBPorts.kext  放到 OpenCore EFI 分区
5. 重启进入MacOS， 打开 Hackintool 工具
    - USB 清理，刷新
    - 验证是否正确识别了协议和速率 (协议正确，插上设备后变绿色)
    - 大部分人到这里基本就解决了, 但还是有一部分人到这里还是不成功, 完全没有任何变化, 请继续看 6
6. 把 `/System/Library/Extensions/IOUSBHostFamily.kext/` 复制出来, 找到包中的 AppleUSBXHCIPCI.kext 驱动, 打开它的 info.plist 文件
    - 再打开刚才定制的 USBPorts.kext 的 info.plist
    - USBPorts 打开 /Root/IOKitPersonalities/xxx-XHCI/IOPCIPrimaryMatch,
    - AppleUSBXHCIPCI 打开 /Root/IOKitPersonalities_x86_64/
    - 产生问题的原因是， 我们的 IOPCIPrimaryMatch 的 ID 不匹配，不在 `IOKitPersonalities_x86_64` 的控制器列表中
    - 找一个 id 相近的驱动， 复制到 我们的 IOKitPersonalities 下， 把id 改成我们的 控制id
    - 重启验证, 如果没正确识别，换另一个驱动试试。



# Resource

https://www.youtube.com/watch?v=-bFuv77zNOY

[USBToolBox](https://github.com/USBToolBox/tool/releases)

[USBToolBox kext](https://github.com/USBToolBox/kext/releases) (似乎不需要)

[WinPE](https://zfile.edgeless.top/Socket) ,  安装盘格式化为 MS-DOS(FAT), GPT (方便安装其它系统，如 Fedora)。 








