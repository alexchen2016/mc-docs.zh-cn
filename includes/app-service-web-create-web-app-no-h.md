使用 [az webapp create](https://docs.azure.cn/zh-cn/cli/webapp?view=azure-cli-latest#az_webapp_create) 命令在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](../articles/app-service/app-service-web-overview.md)。 

在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](../articles/app-service/app-service-web-overview.md)。 可以通过使用 [`az webapp create`](/cli/webapp?view=azure-cli-latest#az_webapp_create) 命令完成此操作。 在以下示例中，将 *\<app_name>* 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。 

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息：

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.chinacloudsites.cn/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.chinacloudsites.cn",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.chinacloudsites.cn/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

已创建了一个空的 Web 应用并启用了 Git 部署。

> [!NOTE]
> Git 远程的 URL 将显示在 `deploymentLocalGitUrl` 属性中，其格式为 `https://<username>@<app_name>.scm.chinacloudsites.cn/<app_name>.git`。 保存此 URL，因为稍后将需要它。
>

浏览到新建的 Web 应用。

```bash
http://<app_name>.chinacloudsites.cn
```

新 Web 应用应该如下所示：

