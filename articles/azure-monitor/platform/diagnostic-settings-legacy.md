---
title: Сбор сведений о журнале действий Azure с помощью параметров диагностики (Предварительная версия) — Azure Monitor | Документация Майкрософт
description: Используйте параметры диагностики для пересылки журналов действий Azure в Azure Monitor журналы, службу хранилища Azure или концентраторы событий Azure.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: fcdcef5d63163b24fe5de0f547dc2dde00cd674f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016261"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Обновление коллекции журналов действий Azure и экспорт
[Журнал действий Azure](platform-logs-overview.md) — это [Журнал платформы](platform-logs-overview.md) , который позволяет получить представление о событиях уровня подписки, произошедших в Azure. Метод отправки записей журнала действий в [концентратор событий или в учетную запись хранения](activity-log-export.md) или в [log Analytics рабочую область](activity-log-collect.md) изменился для использования [параметров диагностики](diagnostic-settings.md). В этой статье описывается различие между методами и способы очистки устаревших параметров в процессе подготовки для изменения параметров диагностики.


## <a name="differences-between-methods"></a>Различия между методами

### <a name="advantages"></a>Преимущества
Использование параметров диагностики имеет следующие преимущества по сравнению с текущими методами:

- Последовательный метод для сбора всех журналов платформы.
- Собирайте журналы действий для нескольких подписок и клиентов.
- Фильтрация коллекции для сбора только журналов для определенных категорий.
- Собирайте все категории журналов действий. Некоторые категории не собираются с помощью устаревшего метода.
- Ускоренная задержка приема журнала. В предыдущем методе задержка составляет около 15 минут, а параметры диагностики добавляются только около 1 минуты.

### <a name="considerations"></a>Рекомендации
Прежде чем включать эту функцию, примите во внимание следующие сведения о сборе журналов действий с помощью параметров диагностики.

- Параметр хранения для сбора журнала действий в службе хранилища Azure был удален, что означает, что данные будут храниться бессрочно до тех пор, пока вы не удалите их.
- Сейчас параметр диагностики уровня подписки можно создать только с помощью портал Azure. Чтобы использовать другие методы, такие как PowerShell или CLI, можно создать шаблон диспетчер ресурсов.


### <a name="differences-in-data"></a>Различия в данных
Параметры диагностики собираются те же данные, что и предыдущие методы, используемые для получения журнала действий со следующими текущими различиями:

Следующие столбцы были удалены. Замена этих столбцов выполняется в другом формате, поэтому может потребоваться изменить запросы к журналу, которые их используют. В схеме можно по-прежнему видеть удаленные столбцы, но они не будут заполнены данными.

| Удаленный столбец | Столбец замены |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| активитисубстатус | активитисубстатусвалуе |
| OperationName     | оператионнамевалуе     |
| ResourceProvider  | ресаурцепровидервалуе  |

Добавлен следующий столбец:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> В некоторых случаях значения в этих столбцах могут быть в верхнем регистре. При наличии запроса, содержащего эти столбцы, следует использовать [оператор = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) для сравнения без учета регистра.

## <a name="work-with-legacy-settings"></a>Работа с устаревшими параметрами
Устаревшие настройки для сбора журнала действий будут продолжать работать, если вы не решите заменить параметром диагностики. Используйте следующий метод, чтобы управлять профилем журнала для подписки.

1. В меню **Azure Monitor** в портал Azure выберите пункт **Журнал действий**.
3. Щелкните **Параметры диагностики**.

   ![Параметры диагностики](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Щелкните сиреневый баннер для устаревшего интерфейса.

    ![Устаревшие возможности](media/diagnostic-settings-subscription/legacy-experience.png)


Дополнительные сведения об использовании устаревших методов сбора см. в следующих статьях.

- [Собирайте и анализируйте журналы действий Azure в Log Analytics рабочей области в Azure Monitor](activity-log-collect.md)
- [Собирайте журналы действий Azure в Azure Monitor между клиентами Azure Active Directory](activity-log-collect-tenants.md)
- [Экспорт журнала действий Azure в хранилище или концентраторы событий Azure](activity-log-export.md)

## <a name="disable-existing-settings"></a>Отключить существующие параметры
Необходимо отключить существующую коллекцию действий, прежде чем включать ее с помощью параметров диагностики. Включение обоих значений может привести к дублированию данных.

### <a name="disable-collection-into-log-analytics-workspace"></a>Отключение сбора в Log Analytics рабочей области

1. Откройте меню **log Analytics рабочие области** в портал Azure и выберите рабочую область, чтобы получить журнал действий.
2. В разделе **Источники данных рабочей области** в меню рабочей области выберите **Журнал действий Azure**.
3. Щелкните подписку, которую нужно отключить.
4. Щелкните **Отключить** , а затем **Да** , когда появится запрос на подтверждение выбора.

### <a name="disable-log-profile"></a>Отключить профиль журнала

1. Используйте процедуру, описанную в разделе [Работа с устаревшими параметрами](#work-with-legacy-settings) , чтобы открыть устаревшие параметры.
2. Отключите все текущие коллекции в хранилище или концентраторах событий.



## <a name="activity-log-monitoring-solution"></a>Решение для мониторинга журнала действий
Решение для мониторинга Log Analytics Azure включает несколько запросов и представлений журнала для анализа записей журнала действий в рабочей области Log Analytics. Это решение использует данные журнала, собранные в Log Analytics рабочей области, и продолжит работать без изменений, если вы собираете журнал действий с помощью параметров диагностики. Дополнительные сведения об этом решении см. в статье [мониторинг журналов действий решение для мониторинга](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Дальнейшие действия

* [См. дополнительные сведения о журнале действий](../../azure-resource-manager/management/view-activity-logs.md)
* [Дополнительные сведения о параметрах диагностики](diagnostic-settings.md)
