---
title: 使用 REST API 实现 Azure 流分析的 CI/CD
description: 了解如何使用 REST API 实现 Azure 流分析的持续集成和部署管道。
services: stream-analytics
author: lingliw
ms.author: v-lingwu
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/21/19
ms.openlocfilehash: 6346a8d213b9825f742099263a57da4ef2bea284
ms.sourcegitcommit: cca72cbb9e0536d9aaddba4b7ce2771679c08824
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58544808"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>使用 API 实现 IoT Edge 流分析的 CI/CD

可使用 REST API 启用 Azure 流分析作业的持续集成和部署管道。 本文举例说明该使用哪些 API 及其具体用法。 Azure Cloud Shell 不支持 REST API。

## <a name="call-apis-from-different-environments"></a>从不同环境调用 API

可从 Linux 和 Windows 调用 REST API。 以下命令演示了调用 API 的正确语法。 本文稍后会介绍特定 API 的用法。

### <a name="linux"></a>Linux

对于 Linux，可使用 `Curl` 或 `Wget` 命令：

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

对于 Windows，请使用 PowerShell： 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url>-Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>在 Edge 上创建 ASA 作业 
 
要创建流分析作业，请使用流分析 API 调用 PUT 方法。

|方法|请求 URL|
|------|-----------|
|PUT|https://management.chinacloudapi.cn/subscriptions/{**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview|
 
使用 curl 的命令示例：

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.chinacloudapi.cn/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
JSON 中的请求正文示例：

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
有关详细信息，请参阅 [API 文档](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)。  
 
## <a name="publish-edge-package"></a>发布 Edge 程序包 
 
要在 IoT Edge 上发布流分析作业，请使用 Edge 程序包发布 API 调用 POST 方法。

|方法|请求 URL|
|------|-----------|
|POST|https://management.chinacloudapi.cn/subscriptions/{**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview|

在作业成功发布前，此异步操作会返回状态 202。 位置响应标头包含用于获取进程状态的 URI。 进程正在运行时，若调用位置标头中的 URI，则会返回状态 202。 进程结束时，位置标头中的 URI 会返回状态 200。 

使用 curl 的 Edge 程序包发布调用示例： 

```bash
curl -d -X POST https://management.chinacloudapi.cn/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
调用 POST 后，应得到一个正文为空的响应。 查找位于响应的 HEAD 中的 URL，并予以记录供将来使用。
 
响应中的 HEAD 的 URL 示例：

```
https://management.chinacloudapi.cn/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
在运行以下命令前等待一到两分钟，先通过在响应的 HEAD 中发现的 URL 调用 API。 如果未获得 200 响应，请重新运行该命令。
 
使用 curl 通过返回的 URL 调用 API 的示例：

```bash
curl -d –X GET https://management.chinacloudapi.cn/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

响应中包括需添加到 Edge 部署脚本的信息。 以下示例显示了需收集的信息，以及要在部署清单中添加信息的位置。
 
成功发布后的响应正文示例：

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}",”PublishTimeStamp”:”{publishtimestamp}”}}}}" 
  status : "Succeeded" 
} 
```

部署清单示例： 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

配置部署清单后，请参阅[使用 Azure CLI 部署 Azure IoT Edge 模块](../iot-edge/how-to-deploy-modules-cli.md)了解有关部署的信息。


## <a name="next-steps"></a>后续步骤 
 
* [Azure IoT Edge 流分析](stream-analytics-edge.md)
* [IoT Edge 教程上的 ASA ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [使用 Visual Studio 工具开发流分析 Edge 作业](stream-analytics-tools-for-visual-studio-edge-jobs.md)
