---
title: Сетка событий Azure. Включение журналов диагностики для раздела
description: В этой статье приводятся пошаговые инструкции по включению журналов диагностики для службы "Сетка событий Azure".
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960507"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Журналы диагностики для статьи службы "Сетка событий Azure"
Параметры диагностики позволяют пользователям сетки событий записывать и просматривать журналы сбоев публикации и доставки в одном из следующих мест: учетная запись хранения Azure, концентратор событий или Рабочая область Log Analytics. В этой статье содержатся пошаговые инструкции по включению журналов диагностики для статьи сетки событий.

## <a name="prerequisites"></a>Технические условия

- Раздел подготовленной сетки событий
- Подготовленное назначение для записи журналов диагностики. Это может быть одно из следующих назначений:
    - Учетная запись хранения Azure.
    - концентратор событий;
    - Рабочая область Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Действия по включению журналов диагностики для раздела

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к разделу сетки событий, для которого необходимо включить параметры журнала диагностики. 
3. Выберите **параметры диагностики** в разделе **мониторинг** в меню слева.
4. На странице **параметры диагностики** выберите **Добавить новый параметр диагностики**. 
    
    ![Кнопка добавления параметра диагностики](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Укажите **имя** параметра диагностики. 

    ![Параметры диагностики — имя](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Включите одно или несколько назначений захвата для журналов, а затем настройте их, выбрав ранее созданный ресурс записи. 
    - Если вы выбрали **архивировать в учетную запись хранения**, выберите **учетная запись хранения — Настройка**, а затем выберите учетную запись хранения в подписке Azure. 

        ![Архивация в учетную запись хранения Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Если выбрать **поток в концентраторе событий**, выберите **концентратор событий — настроить**, а затем — пространство имен концентраторов событий, концентратор событий и политика доступа. 
        ![поток в концентратор событий](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Если вы выбрали **отправить log Analytics**, выберите рабочую область log Analytics.
        ![отправки Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Выберите параметры **деливерифаилурес** и **Публишфаилурес** в разделе **Журнал** . 
    ![выбрать ошибки](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Щелкните **Сохранить**. Щелкните **X** в правом верхнем углу, чтобы закрыть страницу. 
9. Теперь вернитесь на страницу **параметры диагностики** и убедитесь, что в таблице **параметры диагностики** отображается новая запись. 
    ![параметр диагностики в списке](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Вы также можете включить сбор всех метрик для раздела. 

## <a name="next-steps"></a>Дальнейшие действия
Если вам нужна дополнительная помощь, опубликуйте ее на [Stack overflow форуме](https://stackoverflow.com/questions/tagged/azure-eventgrid) или откройте запрос в [службу поддержки](https://azure.microsoft.com/support/options/). 
