## 各硬件设备在 Linux 中的文件名

- 在 Linux 系统中，每个设备都被当成一个 **文件** 来对待
- 在 Linux 系统中，几乎所有的硬件设备文件都在 **`/dev`** 这个目录内

| 设备 | 设备在 Linux 内的文件名 |
| --- | --- |
| IDE 硬盘 | /dev/hd[a-d]  |
| SCSI/SATA/USB 硬盘 | /dev/sd[a-p]  |
| U 硬盘 | /dev/sd[a-p] (与 SATA 相同)  |
| 鼠标 | USB: /dev/usb/mouse[0-15], PS2: /dev/psaux  |

[Linux 内核支持的硬件设备与文件名](https://mirrors.edge.kernel.org/pub/linux/docs/lanana/device-list/devices.txt-3June2001)

## 磁盘分区

- 整块磁盘的 **第一个扇区** 特别重要，它记录了整块磁盘的 2 个重要信息：
  - 主引导分区（Master Boot Record, **MBR**）：可以安装引导加载程序的地方，有 **446 bytes**
  - 分区表（partition table）：记录整块硬盘分区的状态，有 64 bytes

### 磁盘分区表

- 在分区表所在的 **64 bytes** 中，总共分为四组记录区，每组记录区记录了该区段的起始与结束的柱面号码
- 所谓的分区其实就是对这 64 bytes 的分区表进行设置而已
- 硬盘默认的分区表仅能写入 **4** 组分区信息
- 这 4 组分区信息我们称为主（Primary）或扩展（Extended）分区
  - 扩展分区最多只能有一个
  - 逻辑分区是由扩展分区持续切割出来的分区
  - 如果扩展分区被破坏，所有逻辑分区将会被删除
  - 考虑磁盘的连续性，一般建议将扩展分区的柱面号码分配在最后面的柱面内
- 分区的最小单位为 **柱面（Cylinder）**

### 开机流程与主引导分区（MBR）

- 整个开机流程到操作系统之前的动作：
  1. **BIOS**: 开机主动执行的韧体，会认识第一个可开机设备
  2. **MBR**: 第一个可开机设备的第一个扇区内的主引导分区块，**内包含引导加载程序**
  3. **引导加载程序（Boot loader）**：一支可读取内核文件来执行的软件（安装于 MBR 内）
  4. **内核文件**：开始操作系统的功能

- 引导加载程序除了可以安装在 MBR 之外，还可以安装在每个分区的引导扇区（boot sector），这个特色造就 **“多重引导”** 的功能


### 文件挂载

- 所为 **“挂载”** 就是利用一个目录当成进入点，将磁盘分区的数据放置在该目录下，也就是说，进入该目录就可以读取该分区
- 判断某个文件在哪个 partition 可以在目录树中通过反向追踪，看哪个 “进入点” 先被查到那就是使用的进入点了
- 磁盘分区的建议
  - 初次接触 Linux：只要分区 `/` 和 `swap` 即可
  - 预留一个备用的剩余磁盘容量
  - 尽可能将读写频繁的目录与根目录分在不同的区
  - 请特别注意你的服务项目，然后才来进行硬盘的规划
