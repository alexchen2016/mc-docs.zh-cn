---
title: 向远程监视解决方案 UI 添加面板 - Azure | Microsoft Docs
description: 本文介绍如何向远程监视解决方案加速器 Web UI 中的仪表板上添加新面板。
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/05/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: 142110d0c9c4ef85ea6ac149a731c20d69f82a38
ms.sourcegitcommit: 5f2849d5751cb634f1cdc04d581c32296e33ef1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53028508"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>向远程监视解决方案加速器 Web UI 中的仪表板上添加自定义面板

本文介绍如何向远程监视解决方案加速器 Web UI 中的仪表板页上添加新面板。 本文介绍：

- 如何准备本地开发环境。
- 如何在 Web UI 中的仪表板页上添加新面板。

本文的示例面板在现有仪表板页上显示。

## <a name="prerequisites"></a>先决条件

要完成本操作指南中的步骤，需要在本地开发计算机上安装以下软件：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>开始之前

应现完成[将自定义页面添加到远程监控解决方案加速器 Web UI](iot-accelerators-remote-monitoring-customize-page.md) 文章中的步骤，然后再继续操作。

## <a name="add-a-panel"></a>添加面板

要将面板添加到 Web UI，需要添加定义面板的源文件，然后修改仪表板以显示面板。

### <a name="add-the-new-files-that-define-the-panel"></a>添加定义面板的新文件

首先，src/walkthrough/components/pages/dashboard/panels/examplePanel 文件夹包含定义面板的文件，包括：

**examplePanel.js**


将 src/walkthrough/components/pages/dashboard/panels/examplePanel 文件夹复制到 src/components/pages/dashboard/panels 文件夹。

将以下导出添加到 src/walkthrough/components/pages/dashboard/panels/index.js 文件中：

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>将面板添加到仪表板

修改 src/components/pages/dashboard/dashboard.js 以添加面板。

将示例面板添加到面板的导入列表中：

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

将以下单元格定义添加到页面内容中的网格中：

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>测试浮出控件

如果 Web UI 尚未在本地运行，请在存储库的本地副本的根目录中运行以下命令：

```cmd/sh
npm start
```

上述命令在 [http://localhost:3000/dashboard](http://localhost:3000/dashboard) 以本地方式运行 UI。 导航到“仪表板”页查看新面板。

## <a name="next-steps"></a>后续步骤

本文介绍了可以帮助你在远程监视解决方案加速器 Web UI 中添加或自定义仪表板的资源。

有关远程监视解决方案加速器的其他概念性信息，请参阅[远程监视体系结构](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
