> [!NOTE]
> 可以使用 Azure DNS 为 Azure Web 应用配置自定义 DNS 名称。 有关详细信息，请参阅[使用 Azure DNS 为 Azure 服务提供自定义域设置](../articles/dns/dns-custom-domain.md#app-service-web-apps)。
>
>

登录到域提供商的网站。

查找管理 DNS 记录的页面。 每个域提供商都有自己的 DNS 记录界面，因此请查阅提供商的文档。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。 

通常通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到 DNS 记录页面。 转到此页面，然后查找名为**区域文件**、**DNS 记录**或**高级配置**等名称的链接。

以下屏幕截图是 DNS 记录页的一个示例：

![示例 DNS 记录页](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

在示例屏幕截图中，选择“添加”来创建记录。 某些提供商提供了不同的链接来添加不同的记录类型。 同样，请查阅提供商的文档。

> [!NOTE]
> 对于某些提供商（例如 GoDaddy），在选择单独的“保存更改”链接之前，对 DNS 记录的更改不会生效。 
