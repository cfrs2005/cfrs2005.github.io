---
title: Android 启动顺序详解
date: 2024-12-15 08:00:00 +0800
categories: [Technology, Android]
tags: [Android, Startup, Boot Sequence]
img_path: '/assets/img/202412/'
image:
 path: android-startup.webp
 alt: 深入了解 Android 系统的启动顺序及其关键组件。
---

### **总结：Android启动流程表格化**

|**启动阶段**|**作用**|**特点/备注**|
|---|---|---|
|**BootROM**|最初的引导代码，初始化处理器和加载SBL|固化在SoC芯片中，无法修改|
|**POST**|检查基本硬件状态|类似PC的自检，但更快、更简单|
|**SBL（Secondary BootLoader）**|初始化硬件，加载Application BootLoader|提供更高级的硬件初始化|
|**aboot**|加载内核，检测启动模式（正常/恢复/下载）|嵌入式设备的引导加载程序|
|**Primary Boot Mode**|根据按键状态决定启动模式|进入系统、刷机或恢复模式|
|**内核加载（Kernel）**|加载Linux内核和临时根文件系统|初始化硬件，挂载文件系统|
|**init进程**（PID 1）|读取init.rc脚本，启动系统服务和守护进程|Android的特殊init脚本|
|**System/OS**|启动Zygote、System Server等核心进程|完成系统框架加载，显示开机动画|
|**Recovery模式**|执行系统修复和维护|通过特殊按键进入，提供恢复功能|

---


### **1. BootROM**

- **是什么**：  
    BootROM是**只读内存（Read-Only Memory）**中的代码，集成在SoC（System on Chip，系统芯片）内部，由硬件厂商写入。
- **作用**：
    - 检测处理器和内存是否可用。
    - 从存储设备（如eMMC或UFS）中加载第一个启动引导程序（如SBL）。
- **来源**：
    - 嵌入式系统中类似于PC上的**BIOS**，但更小、更精简。
- **关键特点**：
    - BootROM代码是**不可修改**的，提供一个最基础的启动机制。

---

### **2. 处理器初始化和POST**

- **POST全称**：Power On Self Test（电源自检）
- **作用**：
    - 自检系统的基本硬件组件，比如处理器、RAM（内存）等。
    - 检测后将控制权交给启动引导程序。
- **类似于**：PC启动时的POST，但在移动设备上更快、更精简。

---

### **3. SBL（Secondary BootLoader）**

- **全称**：Secondary BootLoader
- **是什么**：SBL是第二阶段的引导程序，由BootROM加载到内存中。
- **作用**：
    - 初始化更多硬件，比如时钟、供电管理等。
    - 加载下一个引导程序（如aboot或操作系统内核）。
- **来源**：厂商定制，不同设备可能有不同的SBL实现。

---

### **4. aboot（Application BootLoader）**

- **全称**：Application BootLoader
- **是什么**：用于加载Android操作系统的引导程序。
- **作用**：
    - 检查**Boot Mode（启动模式）**：
        - **正常启动**：加载内核和系统。
        - **恢复模式（Recovery Mode）**：加载特殊的恢复内核。
        - **下载模式（Bootloader/Download Mode）**：进入刷机或调试状态。
    - 验证内核和系统文件的完整性（如通过签名校验）。
- **类似于**：Linux上的**GRUB2**，但功能更精简、更专注于嵌入式设备。

---

### **5. Primary Boot Mode**

- **是什么**：确定启动模式：
    - **正常模式**：启动Android系统。
    - **下载模式**：通过按键组合进入刷机模式。
    - **恢复模式**：进入**Recovery Kernel**进行系统修复。
- **用来干什么**：根据按键或硬件状态决定后续加载什么程序。
- **用户体验**：例如开机时长按“音量下键 + 电源键”进入刷机模式。

---

### **6. Secondary Boot（内核加载）**

- **作用**：
    - aboot加载Linux内核到内存中。
    - 初始化硬件检测和文件系统挂载。
- **关键文件**：
    - **内核（Kernel）**：负责硬件检测，初始化CPU、内存、设备驱动。
    - **initramfs**（Initial RAM File System）：临时根文件系统，帮助内核挂载真正的根文件系统。
- **Android的文件系统**：
    - 初始化 `/sys`、`/dev`、`/proc` 这些虚拟文件系统，用于系统进程与硬件交互。

---

### **7. init进程（PID 1）**

- **是什么**：Android系统中第一个用户空间进程，和Linux的init相似。
- **作用**：
    - 读取启动脚本 `/init.rc`（Android专有的init配置文件）。
    - 初始化系统服务、守护进程（如Zygote、SurfaceFlinger等）。
    - 启动**Android系统框架**（Framework）。
- **特别之处**：
    - Android的init更加定制化，支持自定义脚本和服务启动逻辑。

---

### **8. 系统加载 (System/OS)**

- **是什么**：Android系统的核心部分，也就是**ROM**（只读存储的操作系统）。
- **作用**：
    - 启动Zygote进程：Zygote是Android的“孵化器”，用于启动和管理应用程序。
    - 启动System Server：管理系统服务，比如窗口管理（SurfaceFlinger）、电源管理等。
- **最终表现**：显示Android开机动画，进入锁屏界面，完成启动。

---

### **9. Recovery模式**

- **是什么**：一个特殊的启动模式，加载一个小型的**恢复内核**和图形界面。
- **作用**：
    - 执行基本系统维护：恢复出厂设置、刷入新系统、清理缓存等。
- **进入方法**：
    - 按特定的组合键进入（例如音量上键+电源键）。
- **技术细节**：
    - Recovery模式运行一个**简化的Linux内核**，具备最基本的硬件驱动和UI界面。

---

---

### **重点总结**

1. **BootROM** → SBL → aboot：逐步引导到内核。
2. **Kernel加载**：初始化硬件，挂载根文件系统。
3. **init进程**：Android系统的启动核心，启动所有服务。
4. **Recovery模式**：特殊模式，用于系统修复。

希望这份详细的解析和表格化内容让你更好理解Android的启动过程。如果有任何不清楚的地方，随时告诉我！