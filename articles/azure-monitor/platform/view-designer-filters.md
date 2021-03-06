---
title: Фильтры в представлениях Azure Monitor | Документация Майкрософт
description: Фильтр в представлении Azure Monitor позволяет пользователям фильтровать данные в представлении по значению определенного свойства, не изменяя само представление.  В этой статье описывается, как использовать фильтр и добавить его в настраиваемое представление.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 03950c7c87f659c5d1c032b5d3c1f74d136697c7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931987"
---
# <a name="filters-in-azure-monitor-views"></a>Фильтры в представлениях Azure Monitor
**Фильтр** в [представлении Azure Monitor](view-designer.md) позволяет пользователям фильтровать данные в представлении по значению определенного свойства, не изменяя само представление.  Например, можно разрешить пользователям представления фильтровать это представление, чтобы просматривать данные только с определенного компьютера или набора компьютеров.  Можно создать несколько фильтров в одном представлении, чтобы разрешить пользователям выполнять фильтрацию по нескольким свойствам.  В этой статье описывается, как использовать фильтр и добавить его в настраиваемое представление.

## <a name="using-a-filter"></a>Использование фильтра
Щелкните диапазон дат и времени в верхней части представления, чтобы открыть раскрывающийся список, в котором можно изменить диапазон дат для представления.

![Пример фильтра](media/view-designer-filters/filters-example-time.png)

Щелкните **+** , чтобы добавить фильтр с помощью настраиваемых фильтров, определенных для представления. Выберите значение для фильтра в раскрывающемся списке или введите его самостоятельно. Продолжайте добавлять фильтры, щелкая **+** . 


![Пример фильтра](media/view-designer-filters/filters-example-custom.png)

Если удалить все значения для фильтра, то он больше не будет применяться.


## <a name="creating-a-filter"></a>Создание фильтра

Создайте фильтр на вкладке **Фильтры** при [редактировании представления](view-designer.md).  Фильтр является глобальным для представления и применяется ко всем его частям.  

![Параметры фильтра](media/view-designer-filters/filters-settings.png)

В следующей таблице описаны параметры фильтра.

| Параметр | Описание |
|:---|:---|
| Имя поля | Имя поля, используемого для фильтрации.  Это поле должно соответствовать полю суммирования в **запросе значений**. |
| Запрос значений | Запрос, выполняемый для заполнения раскрывающегося списка фильтров для пользователя.  Для предоставления уникальных значений конкретному полю этот запрос должен использовать либо [Сводные данные](/azure/kusto/query/summarizeoperator) , либо [DISTINCT](/azure/kusto/query/distinctoperator) . оно должно совпадать с **именем поля**.  Можно использовать оператор [sort](/azure/kusto/query/sortoperator) для сортировки значений, отображаемых для пользователя. |
| Тег | Имя поля, которое используется в запросах с поддержкой фильтра, а также отображается для пользователя. |

### <a name="examples"></a>Примеры

В следующей таблице представлены примеры распространенных фильтров.  

| Имя поля | Запрос значений | Тег |
|:--|:--|:--|
| Компьютер   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Компьютеры |
| EventLevelName | Event &#124; distinct EventLevelName | Серьезность |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Серьезность |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Изменение запросов в представлении

Для вступления фильтра в действие необходимо изменить все запросы в представлении, чтобы применить фильтр по выбранным значениям.  Если не изменить какие бы то ни было запросы в представлении, то любые значения, выбираемые пользователем, не будут действовать.

Для использования значения фильтра в запросе используется приведенный ниже синтаксис. 

    where ${filter name}  

Например, если представление содержит запрос, возвращающий события и использующий фильтр « _Компьютеры_», можно использовать следующий запрос.

    Event | where ${Computers} | summarize count() by EventLevelName

Если вы добавили еще один фильтр, Severity, то можете использовать следующий запрос для применения обоих фильтров.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше об [элементах визуализации](view-designer-parts.md), которые можно добавить в представление.
