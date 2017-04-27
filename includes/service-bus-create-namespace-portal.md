1. 登录到 [Azure 门户][]。

2. 在门户的左侧导航窗格中，依次单击“新建”、“企业集成”和“服务总线”。

4. 在“创建命名空间”  对话框中，输入命名空间名称。 系统会立即检查该名称是否可用。

5. 在确保命名空间名称可用后，选择定价层（基础版、标准版或高级版）。

7. 在“订阅”  字段中，选择要创建命名空间的 Azure 订阅。

9. 在“资源组”  字段中，选择该命名空间将存在于的现有资源组，或者创建一个新资源组。      

8. 在“位置” 中，选择将在其中托管该命名空间的国家或地区。

    ![创建命名空间][create-namespace]

6. 单击“创建” 。 系统现已创建命名空间并已将其启用。 您可能需要等待几分钟，因为系统将为您的帐户配置资源。

### <a name="obtain-the-management-credentials"></a>获取管理凭据

1. 在命名空间列表中，单击新建的命名空间名称。

3. 在命名空间边栏选项卡中，单击“共享访问策略”。

4. 在“共享访问策略”边栏选项卡中，单击“RootManageSharedAccessKey”。

    ![connection-info][connection-info]

5. 在“策略: RootManageSharedAccessKey”边栏选项卡中，单击“连接字符串 - 主键”旁边的复制按钮，将连接字符串复制到剪贴板供以后使用。 将此值粘贴到记事本或其他某个临时位置。

    ![连接字符串][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure 门户]: https://portal.azure.cn