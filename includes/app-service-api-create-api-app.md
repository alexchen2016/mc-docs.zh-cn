使用 [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) 命令在 `myAppServicePlan` 应用服务计划中创建应用。 

该 Web 应用为 API 提供托管空间，并提供一个 URL 用于查看已部署的应用。

在以下命令中，将 *\<app_name>* 替换为唯一名称。 如果 `<app_name>` 不是唯一名称，将收到错误消息“具有给定名称 \<app_name\> 的网站已存在”。 Web 应用的默认 URL 为 `https://<app_name>.chinacloudsites.cn`。 

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.chinacloudsites.cn",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.chinacloudsites.cn",
    "<app_name>.scm.chinacloudsites.cn"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.chinacloudsites.cn",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```