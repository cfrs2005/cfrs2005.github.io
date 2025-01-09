---
title: Linux 启动顺序详解
date: 2024-12-17 08:00:00 +0800
categories: [Technology, Linux]
tags: [Linux, Startup, Boot Sequence]
img_path: '/assets/img/202412/'
image:
 path: linux-startup.webp
 alt: 深入了解 Linux 系统的启动顺序及其关键组件。
---



| **启动阶段**             | **主要内容**              | **关键功能**            | **学习提示/备注**                   |
| -------------------- | --------------------- | ------------------- | ----------------------------- |
| **1. 按下电源按钮**        | 电源供应器启动，CPU重置         | 为启动提供电力，初始化CPU      | 了解硬件启动的基础工作                   |
| **2. 电源自检 (POST)**   | 主板检测硬件（CPU、内存、键盘等）    | 检查硬件健康状态，报告错误       | 蜂鸣声或错误代码指示问题                  |
| **3. BIOS / UEFI**   | 固件启动，初始化硬件            | 加载启动设备（硬盘、光驱等）      | BIOS适用于MBR，UEFI适用于GPT         |
| **4. MBR加载**         | 从硬盘的MBR加载Bootloader代码 | MBR包含分区表和引导代码       | **MBR**（512字节）启动分区信息          |
| **5. Bootloader**    | 启动引导程序 (GRUB等)        | 选择操作系统内核，传递参数给内核    | 常见引导程序：GRUB2、LILO             |
| **6. 内核加载 (Kernel)** | 内核加载到内存，初始化硬件与文件系统    | 初始化CPU、设备驱动，挂载根文件系统 | **initrd/initramfs** 解决驱动加载问题 |
| **7. init进程**        | 启动第一个用户空间进程 (PID=1)   | 读取配置文件，启动系统服务       | **Systemd** 是现代系统管理的主流工具      |
| **8. 服务/守护进程**       | 启动网络、日志、SSH等系统服务      | 完成系统环境初始化，进入用户界面    | 观察 `systemctl` 命令管理服务         |
| **9. 用户空间准备**        | 启动图形界面或命令行界面          | 提供用户登录的环境           | 进入Linux使用环境（TTY或GUI）          |

### **1. POST**

- **全称**：Power On Self Test（上电自检）
- **是什么**：这是计算机开机时的硬件自检过程，检查电脑各个部件是否正常，比如内存、CPU、硬盘、键盘等。
- **来源**：最早来源于计算机硬件设计的需求，确保硬件没有问题才能继续启动。
- **命名起源**：Power On（开机） + Self Test（自我测试），简单直接地描述了它的功能。
- **用来干什么**：检查硬件是否正常工作，如果有问题会通过**蜂鸣声**或屏幕上的错误提示代码报告问题。
- **举个例子**：开机时，如果内存条没插好，你可能听到“滴滴滴”的蜂鸣声，就是POST检测到问题了。

---

### **2. BIOS / UEFI**

|名称|BIOS|UEFI|
|---|---|---|
|**全称**|Basic Input/Output System|Unified Extensible Firmware Interface|
|**是什么**|传统的固件程序，存储在主板上的芯片中|现代化的固件接口，替代BIOS|
|**来源**|1975年IBM公司提出|Intel主导开发，2005年推出|
|**命名起源**|基本输入输出系统，形象描述它的作用|统一可扩展固件接口，更强调“统一”和“可扩展”|
|**用来干什么**|- 初始化硬件 - 加载启动引导程序|- 更强大的硬件初始化 - 支持大容量硬盘|
|**区别**|- 只支持**MBR**分区表 - 2TB硬盘限制|- 支持**GPT**分区表 - 图形界面更友好|
|**CentOS中的体现**|在老旧机器上加载BIOS|现代主板上直接加载UEFI，进入EFI分区|

**举例**：

- 开机时按 **Del** 或 **F2** 键，可以进入BIOS或UEFI设置界面，调整启动顺序。
- 在CentOS中，UEFI会使用一个专门的EFI分区来存放启动文件。

---

### **3. MBR加载**

- **全称**：Master Boot Record（主引导记录）
- **是什么**：位于硬盘的**第一个扇区**（512字节）的引导区域，包含分区表和引导加载程序。
- **来源**：最早用于IBM PC兼容机，用来管理磁盘分区。
- **命名起源**：Master（主）+ Boot（启动）+ Record（记录），意指主启动记录。
- **用来干什么**：
    - 存储分区表信息（硬盘如何被划分）。
    - 加载**Bootloader**，比如GRUB或LILO。
- **CentOS中的体现**：
    - 可以使用 `fdisk -l` 命令查看MBR分区表。
    - 早期的MBR模式分区只能支持最多**2TB**大小的硬盘。

---

### **4. GPT**

- **全称**：GUID Partition Table（全局唯一标识分区表）
- **是什么**：MBR的升级版，用来管理大容量硬盘的分区。
- **来源**：随着硬盘容量增大，MBR的2TB限制无法满足需求，GPT作为UEFI的一部分被引入。
- **命名起源**：GUID（Global Unique Identifier，全局唯一标识）+ Partition Table（分区表）。
- **用来干什么**：
    - 支持超过**2TB**的大硬盘。
    - 分区数量没有限制（MBR只能有4个主分区）。
- **CentOS中的体现**：
    - 可以使用 `parted` 或 `gdisk` 命令查看和管理GPT分区表。

---

### **5. GRUB2**

- **全称**：GNU GRand Unified Bootloader version 2
- **是什么**：Linux系统的主流引导加载程序，用于加载操作系统内核。
- **来源**：由GNU项目开发，是GRUB的第二个版本。
- **命名起源**：GRand Unified（大统一） Bootloader（引导加载器），强调统一管理多个系统的能力。
- **用来干什么**：
    - 提供启动菜单，选择不同的内核或操作系统。
    - 加载Linux内核并传递启动参数。
- **CentOS中的体现**：
    - GRUB2的配置文件在 `/boot/grub2/grub.cfg`。
    - 使用 `grub2-mkconfig` 命令更新配置。

---

### **6. LILO**

- **全称**：Linux Loader
- **是什么**：早期的Linux引导加载程序，功能比GRUB弱，现已很少使用。
- **来源**：1990年代为Linux开发的简单引导程序。
- **命名起源**：Linux的引导加载器（Loader）。
- **用来干什么**：引导Linux内核，但不支持复杂的配置。
- **学习提示**：LILO虽然历史悠久，但已被GRUB完全取代。

---

### **7. initrd / initramfs**

|名称|initrd|initramfs|
|---|---|---|
|**全称**|Initial RAM Disk|Initial RAM File System|
|**是什么**|一个临时根文件系统，用于内核初始化|initrd的升级版，更高效、轻量|
|**来源**|解决内核无法直接访问根文件系统的问题|从initrd演变而来|
|**命名起源**|RAM Disk（内存磁盘）|RAM File System（内存文件系统）|
|**用来干什么**|提供驱动模块，帮助内核挂载根文件系统|同样功能，但性能更好|
|**CentOS中的体现**|文件存放于 `/boot` 目录下，如 `initramfs.img`|使用 `lsinitrd` 命令查看内容|

---

### **8. TTY**

- **全称**：Teletypewriter（电传打字机）
- **是什么**：Linux的虚拟终端接口，用户可以通过命令行访问系统。
- **来源**：名字来自早期的电传打字机，这是一种通过键盘和打印机通信的设备。
- **命名起源**：模拟物理打字机终端的功能，因此命名为TTY。
- **用来干什么**：提供纯文本的命令行界面，用户可以登录并执行操作。
- **CentOS中的体现**：
    - 使用 `Ctrl + Alt + F1` 到 `Ctrl + Alt + F6` 可以切换不同的TTY终端。
    - `tty` 命令可以查看当前终端信息。

---

### **总结：白话串联**

1. **POST**：硬件自检，开机时的“健康检查”。
2. **BIOS/UEFI**：加载启动设备的固件。
3. **MBR/GPT**：分区管理，MBR老旧、GPT现代支持大硬盘。
4. **GRUB2/LILO**：引导加载程序，LILO落伍，GRUB2主流。
5. **initrd/initramfs**：帮助内核加载驱动，挂载根文件系统的临时工具。
6. **TTY**：命令行终端的“窗口”，提供用户登录操作。

---

希望通过表格和白话讲解，这些复杂名词变得易懂！你可以在CentOS系统中实践这些概念，逐步加深理解。如果有具体操作上的问题，随时告诉我！


#linux #启动