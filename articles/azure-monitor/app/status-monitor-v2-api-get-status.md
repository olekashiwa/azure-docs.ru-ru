---
title: Справочник по API агента Azure Application Insights
description: Справочник по API агента Application Insights. Get-Аппликатионинсигхтсмониторингстатус. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9b1010404cb876ed818dd54cf527987c6cf0ffe0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899690"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>API агента Application Insights: Get-Аппликатионинсигхтсмониторингстатус

В этой статье описывается командлет, который является членом [модуля PowerShell AZ. аппликатионмонитор](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Описание

Этот командлет предоставляет сведения об устранении неполадок монитор состояния.
Используйте этот командлет для изучения состояния мониторинга, версии модуля PowerShell и для проверки выполняющегося процесса.
Этот командлет будет сообщать сведения о версии и о файлах ключей, необходимых для мониторинга.

> [!IMPORTANT] 
> Для этого командлета требуется сеанс PowerShell с разрешениями администратора.

## <a name="examples"></a>Примеры

### <a name="example-application-status"></a>Пример: состояние приложения

Выполните команду `Get-ApplicationInsightsMonitoringStatus`, чтобы отобразить состояние мониторинга веб-сайтов.

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

В этом примере —
- **Идентификатор компьютера** — это АНОНИМный идентификатор, используемый для уникальной идентификации сервера. При создании запроса в службу поддержки этот идентификатор потребуется для поиска журналов на сервере.
- **Веб-сайт по умолчанию** остановлен в службах IIS
- **DemoWebApp111** был запущен в службах IIS, но не получил никаких запросов. В этом отчете показано, что нет выполняющегося процесса (ProcessId: не найдено).
- **DemoWebApp222** работает и отслеживается (инструментировано: true). На основе пользовательской конфигурации для этого сайта был сопоставлен ключ инструментирования XXXXXXXX-XXXX-XXXX-XXXX-xxxxxxxxx123.
- **DemoWebApp333** был вручную инструментирован с помощью пакета SDK для Application Insights. Монитор состояния обнаружил пакет SDK и не отслеживает этот сайт.


### <a name="example-powershell-module-information"></a>Пример: сведения о модуле PowerShell

Выполните команду `Get-ApplicationInsightsMonitoringStatus -PowerShellModule`, чтобы отобразить сведения о текущем модуле.

```
PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

### <a name="example-runtime-status"></a>Пример: состояние среды выполнения

Вы можете проверить процесс на инструментированном компьютере, чтобы узнать, загружены ли все библиотеки DLL. Если наблюдение работает, необходимо загрузить не менее 12 библиотек DLL.

Выполните команду `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

## <a name="parameters"></a>Параметры

### <a name="no-parameters"></a>(Нет параметров)

По умолчанию этот командлет выведет отчет о состоянии мониторинга веб-приложений.
Используйте этот параметр, чтобы проверить успешность инструментирования приложения.
Вы также можете проверить, какой ключ инструментирования был сопоставлен с сайтом.


### <a name="-powershellmodule"></a>-Повершеллмодуле
**Необязательно**. Используйте этот параметр, чтобы сообщить номера версий и пути библиотек DLL, необходимых для мониторинга.
Используйте этот параметр, если необходимо указать версию любой библиотеки DLL, включая пакет SDK для Application Insights.

### <a name="-inspectprocess"></a>-Инспектпроцесс

**Необязательно**. Используйте этот параметр для сообщения о том, запущен ли сервер IIS.
Также будут скачаны внешние средства, чтобы определить, загружены ли необходимые библиотеки DLL в среду выполнения IIS.


Если по какой бы то ни было причине этот процесс завершится сбоем, можно выполнить следующие команды вручную:
- Программа iisreset. exe/Status
- [handle64. exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | Findstr/I "Инструментатионенгине AI. ApplicationInsights
- [listdlls64. exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | Findstr/I "Инструментатионенгине AI ApplicationInsights"


### <a name="-force"></a>-Force

**Необязательно**. Используется только с Инспектпроцесс. Используйте этот параметр, чтобы пропустить запрос пользователя, который появляется перед скачиванием дополнительных средств.


## <a name="next-steps"></a>Дальнейшие действия

 Другие действия с агентом Application Insights:
 - Используйте наше справочное по для [устранения неполадок](status-monitor-v2-troubleshoot.md) агента Application Insights.
