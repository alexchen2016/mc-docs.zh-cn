使用 [az webapp source-control config-local-git](https://docs.microsoft.com/cli/azure/webapp/deployment/source#config-local-git) 命令将本地 Git 部署配置到 API 应用。   

应用服务支持采用多种方式将内容部署到 Web 应用，例如 FTP、本地 Git 和 GitHub。 在此快速入门中，将使用本地 Git 进行部署。 这意味着将通过使用 Git 命令从本地存储库推送到 Azure 中的存储库来进行部署。  

使用以下脚本设置在推送代码时将使用的帐户级部署凭据，并确保包含自己的用户名和密码值。   

```azurecli
az webapp deployment user set --user-name <desired user name> --password <desired password>
```