| 资源 | 免费 | 共享（预览） | 基本 | 标准 | 高级 </th> |
| --- | --- | --- | --- | --- | --- |
| 每个[应用服务计划](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)的 [Web 应用、移动应用或 API 应用数](/app-service/)<sup>1</sup> |10 个 |100 |无限制<sup>2</sup> |无限制<sup>2</sup> |无限制<sup>2</sup> |
| 每个[应用服务计划](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)的[逻辑应用数](/logic-apps/)</a><sup>1</sup> |10 个 |10 个 |10 个 |每个核心 20 个 |每个核心 20 个 |
| [应用服务计划](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |每个区域 1 个 |每个资源组 10 个 |每个资源组 100 个 |每个资源组 100 个 |每个资源组 100 个 |
| 计算实例类型 |共享 |共享 |专用<sup>3</sup> |专用<sup>3</sup> |专用<sup>3</sup></p> |
| [横向扩展](../articles/app-service/web-sites-scale.md)（最大实例数） |1 个共享 |1 个共享 |3 个专用<sup>3</sup> |10 个专用<sup>3</sup> |20 个专用（50 个在 ASE 中）<sup>3,4</sup> |
| 存储<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| CPU 时间（5 分钟）<sup>6</sup> |3 分钟 |3 分钟 |无限制，按标准[费率](https://www.azure.cn/pricing/details/app-service/)</a>付费 |无限制，按标准费率付费 |无限制，按标准费率付费 |
| CPU 时间（天）<sup>6</sup> |60 分钟 |240 分钟 |无限制，按标准[费率](https://www.azure.cn/pricing/details/app-service/)</a>付费 |无限制，按标准费率付费 |无限制，按标准费率付费 |
| 内存（1 小时） |每个应用服务计划 1024 MB |每个应用 1024 MB |不适用 |不适用 |不适用 |
| 带宽 |165 MB |无限制，收取[数据传输费](https://www.azure.cn/pricing/details/data-transfer/) |无限制，收取数据传输费率 |无限制，收取数据传输费率 |无限制，收取数据传输费率 |
| 应用程序体系结构 |32 位 |32 位 |32 位/64 位 |32 位/64 位 |32 位/64 位 |
| 每个实例的 Web 套接字数<sup>7</sup> |5 |35 |350 |无限制 |不受限制 |
| 每个应用程序的并发[调试器连接数](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) |1 |1 |1 |5 |5 |
| [带 FTP/S 和 SSL 的 chinacloudsites.cn 子域](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [自定义域](../articles/app-service/app-service-web-tutorial-custom-domain.md)支持 | |X |X |X |X |
| 自定义域 [SSL 支持](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |无限制的 SNI SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 |
| 集成负载均衡器 | |X |X |X |X |
| [始终打开](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [计划备份](../articles/app-service/web-sites-backup.md) | | | | 计划每 2 小时备份一次，每天最多 12 个备份（手动 + 计划） | 计划每小时备份一次，每天最多 50 个备份（手动 + 计划） |
| [自动缩放](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |
| [Azure 计划程序](/scheduler/)支持 | |X |X |X |X |
| [终结点监视](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [过渡槽](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 个 |
| 每个应用的自定义域数</a> | |500 |500 |500 |500 |
| SLA | |<p> |99.9% |99.95%<sup>10</sup> |99.95%<sup>9</sup> |

<sup>1</sup>除非特别说明，否则应用和存储配额依每个应用服务计划为准。  
<sup>2</sup>可以在这些计算机上托管的应用的实际数目取决于应用的活动、计算机实例的大小和相应的资源利用率。  
<sup>3</sup>专用实例可有不同的大小。 有关详细信息，请参阅[应用服务定价](https://www.azure.cn/pricing/details/app-service/)。
<sup>4</sup>高级层在使用应用服务环境时最多允许 50 个计算实例（取决于可用性）和 500 GB 的磁盘空间，否则为 20 个计算实例和 250 GB 的存储。  
<sup>5</sup>存储限制是跨相同应用服务计划中所有应用的内容总大小。
<sup>6</sup>这些资源受到专用实例上的物理资源（实例大小和实例数）的限制。  
<sup>7</sup>如果你将基本层的某个应用扩展为两个实例，则其中每个实例有 350 个并发连接。  
<sup>8</sup>按需、按计划或作为应用服务实例内的后台任务连续运行自定义可执行文件和/或脚本。 连续执行 WebJob 需要使用“始终打开”。 计划的 WebJob 需要使用 Azure 计划程序免费或标准版。 可以在应用服务实例中运行的 WebJob 的数量没有预定义的限制，但是存在实际限制，这些限制取决于应用程序代码尝试执行的任务。   
<sup>9</sup>向使用多个实例和为故障转移配置的 Azure 流量管理器的部署提供 99.95% 的 SLA。  


<!-- ms.date: 10/26/2017 -->