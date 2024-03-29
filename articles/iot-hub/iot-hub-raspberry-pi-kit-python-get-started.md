---
title: 连接到云的 Raspberry Pi (Python) - 将 Raspberry Pi 连接到 Azure IoT 中心 | Microsoft Docs
description: 在本教程中了解如何设置 Raspberry Pi 并将其连接到 Azure IoT 中心，使其能够将数据发送到 Azure 云平台。
author: rangv
manager: timlt
tags: ''
keywords: Azure IoT Raspberry Pi, Raspberry Pi IoT 中心, Raspberry Pi 将数据发送到云, 连接到云的 Raspberry Pi
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 04/11/2018
ms.author: v-yiso
ms.date: 10/08/2018
ms.openlocfilehash: f4882080d1a33e1cfd3a67218c40bea99c5f2afc
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58626249"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>将 Raspberry Pi 连接到 Azure IoT 中心 (Python)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教程中，首先学习有关使用运行 Raspbian 的 Raspberry Pi 的基础知识。 然后学习如何使用 [Azure IoT 中心](about-iot-hub.md)将设备无缝连接到云。 有关 Windows 10 IoT Core 的示例，请访问 [Windows 开发人员中心](http://www.windowsondevices.com/)。

还没有工具包？ 试用 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)。 或在[此处](https://docs.azure.cn/zh-cn/develop/iot/iot-starter-kits)购买新工具包。

## <a name="what-you-do"></a>准备工作

* 创建 IoT 中心。
* 在 IoT 中心内为 Pi 注册设备。
* 设置 Raspberry Pi。
* 在 Pi 上运行示例应用程序，以将传感器数据发送到 IoT 中心。

将 Raspberry Pi 连接到所创建的 IoT 中心。 然后，在 Pi 上运行示例应用程序，从 BME280 传感器收集温度和湿度数据。 最后，将传感器数据发送到 IoT 中心。

## <a name="what-you-learn"></a>学习内容

* 如何创建 Azure IoT 中心以及如何获取新的设备连接字符串。
* 如何通过 BME280 传感器连接 Pi。
* 如何通过在 Pi 上运行示例应用程序来收集传感器数据。
* 如何将传感器数据发送到 IoT 中心。

## <a name="what-you-need"></a>需要什么

![需要什么](./media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 或 Raspberry Pi 3 电路板。
* 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需花费几分钟就能[创建一个 Azure 试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。
* 连接到 Pi 的监视器、USB 键盘和鼠标。
* 运行 Windows 或 Linux 的 Mac 或电脑。
* Internet 连接。
* 16 GB 或更大容量的 microSD 卡。
* USB-SD 适配器或 microSD 卡，用于将操作系统映像刻录到 microSD 卡中。
* 带有 6 英尺微型 USB 电缆的 5 伏 2 安电源。

以下项可选：

* 已装配的 Adafruit BME280 温度、压力和湿度传感器。
* 试验板。
* 6 根 F/M 跳线。
* 散射的 10 毫米 LED 灯。


> [!NOTE]
> 上述项可选，因为代码示例支持模拟的传感器数据。


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>设置 Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>为 Pi 安装 Raspbian 操作系统

准备用于安装 Raspbian 映像的 microSD 卡。

1. 下载 Raspbian。
   1. [下载 Raspbian Jessie with Desktop](https://www.raspberrypi.org/downloads/raspbian/)（.zip 文件）。
   1. 将 Raspbian 映像解压缩到计算机的某个文件夹中。
1. 将 Raspbian 安装到 microSD 卡。
   1. [下载并安装 Etcher SD 卡刻录机实用工具](https://etcher.io/)。
   1. 运行 Etcher 并选择已在步骤 1 中解压缩的 Raspbian 映像。
   1. 选择 microSD 卡驱动器。 注意，Etcher 可能已选择了正确的驱动器。
   1. 单击“刷机”，将 Raspbian 安装到 microSD 卡。
   1. 在安装完成后，从计算机中移除 microSD 卡。 可以安全地直接取出 microSD 卡，因为 Etcher 会在完成后自动弹出或卸载 microSD 卡。
   1. 将 microSD 卡插入 Pi。

### <a name="enable-ssh-and-i2c"></a>启用 SSH 和 I2C

1. 将 Pi 连接到监视器、键盘和鼠标，启动 Pi，然后通过将 `pi` 用作用户名并将 `raspberry` 用作密码来登录 Raspbian。
1. 依次单击 Raspberry 图标 >“首选项” > “Raspberry Pi 配置”。

   ![Raspbian 首选项菜单](./media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. 在“接口”选项卡上，将“I2C”和“SSH”设置为“启用”，然后单击“确定”。 如果没有物理传感器并且想要使用模拟的传感器数据，则此步骤是可选的。

   ![在 Raspberry Pi 上启用 I2C 和 SSH](./media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> 若要启用 SSH 和 I2C，可在 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 和 [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) 中找到更多参考文档。

### <a name="connect-the-sensor-to-pi"></a>将传感器连接到 Pi

使用试验板和跳线，将 LED 灯和 BME280 连接到 Pi，如下所示。 如果没有该传感器，请[跳过此部分](#connect-pi-to-the-network)。

![Raspberry Pi 和传感器连接](./media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

BME280 传感器可以收集温度和湿度数据。 如果设备和云之间有通信，LED 将闪烁。 

对于传感器引脚，请使用以下接线：

| 始端（传感器和 LED 灯）     | 结束（开发板）            | 线缆颜色   |
| -----------------------  | ---------------------- | ------------: |
| VDD（引脚 5G）             | 3.3V 电源（引脚 1）       | 白线   |
| GND（引脚 7G）             | GND（引脚 6）            | 棕色电缆   |
| SDI（引脚 10G）            | I2C1 SDA（引脚 3）       | 红线     |
| SCK（引脚 8G）             | I2C1 SCL（引脚 5）       | 橙色电缆  |
| LED VDD（引脚 18F）        | GPIO 24（引脚 18）       | 白线   |
| LED GND（引脚 17F）        | GND（引脚 20）           | 黑线   |

单击查看 [Raspberry Pi 2 和 3 引脚映射](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi)以供参考。

成功将 BME280 连接到 Raspberry Pi 后，它应如下图所示。

![连接在一起的 Pi 和 BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>将 Pi 连接到网络

使用 USB 微电缆和电源开启 Pi。 使用以太网电缆将 Pi 连接到有线网络，或者按照 [Raspberry Pi Foundation 中的说明](https://www.raspberrypi.org/learning/software-guide/wifi/)将 Pi 连接到无线网络。 将 Pi 成功连接到网络后，需要记下 [Pi 的 IP 地址](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)。

![已连接到有线网络](./media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> 确保 Pi 与计算机连接到同一网络。 例如，如果计算机连接到无线网络，而 Pi 连接到有线网络，则在 devdisco 输出中可能看不到 IP 地址。

## <a name="run-a-sample-application-on-pi"></a>在 Pi 上运行示例应用程序

### <a name="install-the-prerequisite-packages"></a>安装必备组件包

使用主计算机的以下任一 SSH 客户端连接到 Raspberry Pi。
   
   **Windows 用户**
   1. 下载并安装 [PuTTY](http://www.putty.org/) for Windows。 
   1. 将 Pi 的 IP 地址复制到主机名（或 IP 地址）部分，并选择 SSH 作为连接类型。
   
   
   **Mac 和 Ubuntu 用户**
   
   使用 Ubuntu 或 macOS 上的内置 SSH 客户端。 可能需要运行 `ssh pi@<ip address of pi>`，以通过 SSH 连接 Pi。
> [!NOTE]
>    默认用户名是 `pi`，密码是 `raspberry`。


### <a name="configure-the-sample-application"></a>配置示例应用程序

1. 通过运行以下命令，克隆示例应用程序：

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-python-raspberrypi-client-app.git
   ```
1. 通过运行以下命令，打开配置文件：

   ```bash
   cd iot-hub-python-raspberrypi-client-app
   nano config.py
   ```

   此文件中有 5 个可配置的宏。 第一个是 `MESSAGE_TIMESPAN`，它确定发送到云的两条消息之间的时间间隔（以毫秒为单位）。 第二个是 `SIMULATED_DATA`，它是一个布尔值，指示是否使用模拟的传感器数据。 `I2C_ADDRESS` 是 BME280 传感器连接的 I2C 地址。 `GPIO_PIN_ADDRESS` 是 LED 的 GPIO 地址。 最后一个为 `BLINK_TIMESPAN`，它定义打开 LED 的时间跨度（以毫秒为单位）。

   如果**没有传感器**，请将 `SIMULATED_DATA` 值设置为 `True`，使示例应用程序创建和使用模拟的传感器数据。

1. 通过按“Ctrl-O”>“Enter”>“Ctrl-X”保存并退出。

### <a name="build-and-run-the-sample-application"></a>生成并运行示例应用程序

1. 通过运行以下命令，生成示例应用程序。 因为用于 Python 的 Azure IoT SDK 是 Azure IoT 设备 C SDK 顶层的包装器，所以如果想要或需要从源代码生成 Python 库，则需要编译 C 库。

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE]
   > 还可以通过运行 `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]` 指定所需版本。 如果运行不带参数的脚本，此脚本会自动检测安装的 python 的版本（搜索序列 2.7->3.4->3.5）。 请确保 Python 版本在生成和运行期间保持一致。 
   > 
   > [!NOTE]
   > 在内存小于 1GB RAM 的 Linux 设备上生成 Python 客户端库 (iothub_client.so) 时，可能会看到在生成 iServ_client_python.cpp 时进度在 98% 处停滞，如下所示 `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`。 如果遇到此问题，在此期间，请在另一终端窗口中使用 `free -m command` 检查设备的内存使用情况。 如果在编译 iothub_client_python.cpp 文件时出现内存不足的情况，则必须临时增加交换空间以获得更多可用内存，才能成功生成 Python 客户端设备 SDK 库。
   
2. 通过运行以下命令，生成示例应用程序：

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE]
   > 确保将设备连接字符串复制并粘贴到单引号中。 如果使用的是 python 3，则可以使用命令 `python3 app.py '<your Azure IoT hub device connection string>'`。


   应看到以下输出，其中显示传感器数据以及发送至 IoT 中心的消息。

   ![输出 - 从 Raspberry Pi 发送到 IoT 中心的传感器数据](media/iot-hub-raspberry-pi-kit-c-get-started/success.png)

## <a name="next-steps"></a>后续步骤

此时已运行示例应用程序，收集传感器数据并将其发送到 IoT 中心。 若要查看 Raspberry Pi 已发送到 IoT 中心的消息或要向 Raspberry Pi 发送消息，请参阅[使用用于 Visual Studio Code 的 Azure IoT 工具包扩展在设备和 IoT 中心之间发送和接收消息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!--Update_Description:update meta properties and wording-->