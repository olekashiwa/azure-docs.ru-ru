---
title: Оповещения для журнала действий в Azure Monitor
description: Уведомление посредством SMS, веб-перехватчика, электронной почты и других средств при возникновении определенных событий в журнале действий.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/17/2018
ms.openlocfilehash: c42f72800a93de714f0cc126939a28a8a6b5fce4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747528"
---
# <a name="alerts-on-activity-log"></a>Оповещения на основе журнала действий 

## <a name="overview"></a>Обзор
Оповещения журнала действий — это оповещения, которые активируются при возникновении нового [события журнала действий](activity-log-schema.md) , соответствующего условиям, указанным в предупреждении. В зависимости от порядка и объема событий, записанных в [Журнал действий Azure](platform-logs-overview.md), будет срабатывать правило генерации оповещений. Правила генерации оповещений журнала действий являются ресурсами Azure, поэтому их можно создать с помощью шаблона Azure Resource Manager. Их также можно создать, обновить или удалить на портале Azure. В этой статье описаны принципы работы оповещений журнала действий. Дополнительные сведения о создании и использовании правил генерации оповещений журнала действий см. в статье [Создание оповещений журнала действий и управление ими](alerts-activity-log.md).

> [!NOTE]
> **Невозможно** создать оповещения для событий в категории "оповещения" журнала действий.

Обычно оповещения журнала действий создаются для получения уведомлений в таких случаях:

* При выполнении определенных операций с ресурсами в вашей подписке Azure. Как правило, это конкретные группы ресурсов или ресурсы. Например, можно получать уведомления при удалении любой виртуальной машины в группе ресурсов myProductionResourceGroup. Или может потребоваться получать уведомления при назначении каких-либо новых ролей пользователю в вашей подписке.
* Возникновение события работоспособности службы. События работоспособности службы включают в себя уведомления об инцидентах и событиях обслуживания, относящихся к ресурсам в подписке.

Простая аналогия для понимания условий, при которых правила генерации оповещений могут быть созданы в журнале действий, заключается в просмотре или отфильтруйте события с помощью [журнала действий в портал Azure](activity-log-view.md#azure-portal). В журнале действий Azure Monitor можно отфильтровать или найти необходимое событие, а затем создать оповещение с помощью кнопки **Добавить оповещение журнала действий** .

В любом случае оповещения журнала действий позволяют отслеживать только события в подписке, в которой создается оповещение.

Оповещения журнала действий можно настроить на основе любого свойства верхнего уровня в объекте JSON для события журнала действий. Дополнительные сведения см. [в разделе категории в журнале действий](activity-log-view.md#categories-in-the-activity-log). Дополнительные сведения о событиях службы работоспособности см. в статье [Создание оповещений журнала действий для уведомлений службы](alerts-activity-log-service-notifications.md). 

Оповещения журнала действий имеют несколько стандартных параметров.

- **Категория**. Возможные значения: "Административный", "Работоспособность службы", "Автомасштабирование", "Безопасность", "Политика" и "Рекомендация". 
- **Область** — отдельный ресурс или набор ресурсов, для которого определено оповещение в журнале действий. Область для оповещения журнала действий можно определить на разных уровнях:
    - уровень ресурсов — например, для конкретной виртуальной машины;
    - уровень группы ресурсов — например, все виртуальные машины в определенной группе ресурсов;
    - уровень подписки — например, все виртуальные машины или все ресурсы в подписке.
- **Группа ресурсов**. По умолчанию правило генерации оповещений сохраняется в группе ресурсов, которая определена в качестве целевой в области. Пользователь также может определить группу ресурсов, в которой должно храниться правило генерации оповещений.
- **Тип ресурса**. Пространство имен, определенное в Resource Manager, для целевого объекта оповещения.
- **Имя операции**. имя [операции Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md) используется для управления доступом на основе ролей. Операции, не зарегистрированные в Azure Resource Manager, нельзя использовать в правиле оповещения журнала действий.
- **Уровень.** Уровень серьезности события ("Подробный", "Информационное", "Предупреждение", "Ошибка" или "Критическое").
- **Состояние.** Состояние события, обычно "Запущено", "Сбой" или "Успешно".
- **Кем инициировано событие.** Это условие также называется "вызывающей стороной". Адрес электронной почты или идентификатор Azure Active Directory пользователя, выполнившего операцию.

> [!NOTE]
> Подписка позволяет создать до 100 правил оповещений для действия, имеющих одну из следующих областей: один ресурс, все ресурсы в группе ресурсов или вся подписка.

При активации оповещения журнала действий используется группа действий для создания действий или уведомлений. Группа действий — это многократно используемый набор приемников уведомлений, таких как адреса электронной почты, URL-адреса веб-перехватчиков или номера телефонов для отправки SMS. На приемники можно ссылаться из нескольких уведомлений, чтобы централизовать и группировать каналы уведомлений. Есть два способа определения оповещения журнала действий. Вы сможете:

* Использовать имеющуюся группу действий в оповещении журнала действий.
* Создать группу действий.

Дополнительные сведения о группах действия см. в статье [Создание групп действий и управление ими на портале Azure](action-groups.md).


## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [обзором оповещений](alerts-overview.md).
- Узнайте, как [создавать и изменять оповещения для журнала действий](alerts-activity-log.md).
- Просмотрите схему веб-перехватчика оповещений журнала действий в статье [Объекты webhook для оповещений журнала действий Azure](activity-log-alerts-webhook.md).
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](service-notifications.md).
