---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: f250c20b0221743c12f95e30676d4773cf40aeb4
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52676235"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>通过模板标记虚拟机
首先，让我们看一下如何通过模板进行标记。 [此模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)将标记放置在以下资源中：计算（虚拟机）、存储（存储帐户）和网络（公共 IP 地址、虚拟网络和网络接口）。 此模板适用于 Windows VM，但经过改造后也可用于 Linux VM。

单击 [模板链接](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) 中的 **部署至 Azure** 按钮。 此操作将导航到 [Azure 门户](https://portal.azure.cn/)，可在其中部署此模板。

![使用标记进行简单部署](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

此模板包括以下标记：*Department*、*Application* 和 *Created By*。 如果想要不同的标记名称，则可以直接在模板中添加/编辑这些标记。

![模板中的 Azure 标记](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

如你所见，标记定义为键值对，用冒号 (:) 分隔。 必须按以下格式定义标记：

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

完成编辑后，使用选择的标记保存模板文件。

接下来，在  “编辑参数”部分中，可以填写标记的值。

![通过 Azure 门户编辑标记](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

单击  “创建”，使用标记值部署此模板。

## <a name="tagging-through-the-portal"></a>通过门户进行标记
使用标记创建资源后，可以在门户中查看、添加和删除该标记。

选择标记图标，以查看标记：

![Azure 门户中的标记图标](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

通过定义自己的键/值对，使用门户添加新标记并进行保存。

![通过 Azure 门户添加新标记](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

新标记现在应在资源的标记列表中显示。

![Azure 门户中保存的新标记](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

<!-- Update_Description: update meta properties, wording update -->