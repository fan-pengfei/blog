---
title: "F1C100S驱动开发"
date: 2022-10-02T04:23:19.000Z
draft: false
tags: ["F1C100S"]
---
> Uboot和系统跑起来了，接下来就是进行驱动开发了；

## Heartbeat(心跳灯)

### 修改设备树：

在设备树文件`arch/arm/boot/dts/sun8i-v3s-licheepi-zero.dts`中；

在 / { } 所包裹的根节点目录下添加：

```plaintext
leds {
    compatible = "gpio-leds";
    user_led {
    label = "led:usr";
        gpios = ; /* PE12 */
    };
};
```

其中

```plaintext
gpios = ; /* PE12 */
```

代表引脚 `4 * 32 + 12`也就是`PE12（ A~G： 0~6）`
其名字为：`led:usr`；

系统启动后与LED控制有关的文件；

系统启动后，将看到这样的文件：

```bash
$: ls /sys/class/leds/
led:usr
```

这里三个文件夹分别对应设备树中定义的三个LED；

### 编译设备树：

> 可以只编译设备树，不编译其他文件；

```bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- dtbs -j12
```

### 控制LED灯亮灭：

点亮LED：

```bash
$: echo 1 > /sys/class/leds/led\:usr/brightness
```

熄灭LED：

```bash
$: echo 0 > /sys/class/leds/led\:usr/brightness
```

控制LED闪烁：

```bash
$: ls /sys/class/leds/led\:usr/
brightness      invert          power           trigger
device          max_brightness  subsystem       uevent
$: cat /sys/class/leds/led\:usr/trigger
none kbd-scrolllock kbd-numlock kbd-capslock kbd-kanalock kbd-shiftlock kbd-altgrlock kbd-ctrllock kbd-altlock kbd-shiftllock kbd-shiftrlock kbd-ctrlllock kbd-ctrlrlock mmc0 [heartbeat] default-on
#这里可以看到当前的值为none，表示没有trigger，将其值改成heartbeat就可以看到闪烁了
$: echo heartbeat > /sys/class/leds/led\:usr/trigger
```

### 开机自动打开心跳灯：

> 作为系统服务自动启动，在这个目录添加脚本文件 `/etc/init.d/`；
> 作为登录用户的自动启动程序，在 `/etc/profile.d/` 添加脚本文件；

新建脚本文件`heartbeat_led.sh`：

```bash
echo heartbeat >/sys/class/leds/led\:usr/trigger
```

将该脚本放置于 `/etc/profile.d/`中即可开机自动调用该脚本：

```shell
$: ls /etc/profile.d/
heartbeat_led.sh  umask.sh
```

## F1C100S GPIO操作：

```bash
#使用sysfs操作GPIO的例子：
echo 192 > /sys/class/gpio/export  #导出 PG0, GREEN
ls /sys/class/gpio/
export     gpio192    gpiochip0  unexport
ls /sys/class/gpio/gpio192/
active_low direction subsystem/ value device/ power/ uevent
echo "out" > /s
ys/class/gpio/gpio192/direction #设置为输出
echo 0 > /sys/class/gpio/gpio192/value     #亮灯
echo 1 > /sys/class/gpio/gpio192/value #灭灯
echo "in" > /sys/class/gpio/gpio192/direction #设置为输入
cat /sys/class/gpio/gpio192/value #读取电平
0
```

### 引脚计算规则：

> 在Linux中，GPIO 使用0～MAX_INT之间的整数标识；
> 对于32位CPU，每组GPIO 32个，引脚号就是按顺序排列；
> 从PA0开始gpio是0，那么PE3对应是32*4+3=131，经试验已验证；

这个板子是PE12引脚为LED引脚，故：

PIN_{num}=32\times4+12=140
### 故点灯的脚本如下：

```bash
echo "out" > /sys/class/gpio/gpio140/direction #设置为输出
#死循环
while true
do
    echo 0 > /sys/class/gpio/gpio140/value
    echo "点亮"
    sleep 0.1
    echo 1 > /sys/class/gpio/gpio140/value
    sleep 0.1
    echo "熄灭"
done
```

## 文件系统打包和解压缩（rootfs.tar）：

### 压缩：

```bash
sudo tar -cvf rootfs.tar ./
```

### 解压：

```bash
sudo tar -xf ./rootfs.tar -C /mnt/
```

### 解压到SD卡中：

```bash
#如果分区已挂载到别的地方先进行卸载
sudo umount /dev/sdb2
#将分区挂载到 /mnt
sudo mount /dev/sdb2 /mnt
#解压并拷贝rootfs
cd ~/f1c100s-sdk/buildroot-2022.02/
sudo tar -xf output/images/rootfs.tar -C /mnt/
#保存退出
sync
sudo umount /dev/sdb2
```

## 复制.so文件到系统中：

### From：

```bash
#虚拟机
/usr/arm-linux-gnueabi/lib
```

### To：

```bash
#嵌入式Linux系统
/usr/lib
```

> 有名称相同的文件，跳过即可，不能覆盖；

## 关机操作：

需要注意的是，在开发板运行过程中，如果想要重启，请先执行：

```bash
poweroff
```

命令正常关闭系统后，在按重启按钮，否则有很大概率回造成文件系统损坏；
