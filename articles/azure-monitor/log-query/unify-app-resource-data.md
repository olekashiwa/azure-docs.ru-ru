---
title: Объединение нескольких ресурсов Azure Monitor Application Insights | Документация Майкрософт
description: В этой статье содержатся сведения по использованию функции журналов Azure Monitor для запроса нескольких ресурсов Application Insights и визуализации данных.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137492"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Объединение нескольких ресурсов Azure Monitor Application Insights 
В этой статье описывается, как запрашивать и просматривать все данные журнала Application Insights в одном месте, даже если они находятся в разных подписках Azure, в качестве замены нерекомендуемых Соединитель Application Insights. Количество Application Insights ресурсов, которые можно включить в один запрос, ограничено 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Рекомендуемый подход для запроса нескольких ресурсов Application Insights 
Перечисление нескольких ресурсов Application Insights в запросе может быть весьма трудоемким и трудным в поддержке процессом. Вместо этого вы можете использовать функции для отделения логики запросов и области приложений.  

В примере ниже показано, как отслеживать несколько ресурсов Application Insights и визуализировать количество неудачных запросов по имени приложения.

Создайте функцию с помощью оператора union со списком приложений, затем сохраните запрос в рабочей области в качестве функции с псевдонимом *applicationsScoping*. 

Вы можете в любое время изменить список приложений на портале, перейдя к обозревателю запросов в рабочей области и выбрав функцию для редактирования с последующим сохранением, или с помощью командлета PowerShell `SavedSearch`. 

>[!NOTE]
>Этот метод нельзя использовать с оповещениями журнала, так как проверка доступа к ресурсам правила генерации оповещений, включая рабочие области и приложения, выполняется во время создания предупреждения. Добавление новых ресурсов в функцию после создания предупреждения не поддерживается. Если вы предпочитаете использовать функцию для определения области ресурсов в оповещениях журнала, необходимо изменить правило генерации оповещений на портале или с помощью шаблона диспетчер ресурсов, чтобы обновить ресурсы с областью действия. Кроме того, можно включить список ресурсов в запрос оповещения журнала.

Команда `withsource= SourceApp` позволяет добавить в результаты столбец, который обозначает приложение, отправившее журнал. Оператор Parse необязателен в этом примере и использует для извлечения имени приложения из свойства Саурцеапп. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Теперь вы готовы использовать функцию applicationsScoping в межресурсном запросе.  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Запрос использует схему Application Insights, несмотря на то что запрос выполняется в рабочей области, так как функция applicationsScoping возвращает структуру данных Application Insights. Псевдоним функции возвращает объединение запросов из всех определенных приложений. После этого с помощью запроса фильтруются неудачные запросы и визуализируются тенденции по приложению.

![Пример результатов по нескольким запросам](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[Запрос между разными ресурсами](../log-query/cross-workspace-query.md) в оповещениях журнала поддерживается в новом API [правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). По умолчанию Azure Monitor использует [устаревшие API оповещения Log Analytics](../platform/api-alerts.md) для создания любого нового правила генерации оповещений на портале Azure, пока вы не переключаетесь с [устаревших API оповещений журнала](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). После переключения новый API используется по умолчанию для новых правил генерации оповещений на портале Azure и позволяет создавать правила генерации оповещений журнала запроса между разными ресурсами. Вы можете создать правила генерации оповещений журнала [запросов между разными ресурсами](../log-query/cross-workspace-query.md), не переключаясь, с помощью [шаблона ARM для API правил запросов по расписанию](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template), но этим правилом оповещения можно управлять с помощью [API правил запросов по расписанию](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), а не с помощью портала Azure.

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Различия схем рабочей области Application Insights и Log Analytics
В следующей таблице показаны различия схем Log Analytics и Application Insights.  

| Свойства рабочей области Log Analytics| Свойства ресурса Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | длительность |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | идентификатор |
| AvailabilityTestName | name |
| AvailabilityTimestamp | TIMESTAMP |
| Браузер | client_browser |
| Город | client_city |
| ClientIP | client_IP |
| Компьютер | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | длительность | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | длительность | 
| RequestID | идентификатор | 
| RequestName | name | 
| RequestSuccess | Успешное завершение | 
| ResponseCode | resultCode | 
| Роль | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL-адрес | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Дальнейшие действия

Используйте [поиск по журналам](../../azure-monitor/log-query/log-query-overview.md), чтобы просматривать подробные сведения о приложениях Application Insights.
