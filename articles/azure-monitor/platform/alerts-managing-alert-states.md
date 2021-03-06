---
title: Управление состоянием оповещений и смарт-групп
description: Управление состоянием экземпляров оповещений и смарт-групп
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: fd2a2a4b7d5033ecd35d02baa240bc7586e7a25d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555666"
---
# <a name="manage-alert-and-smart-group-states"></a>Управление состоянием оповещений и смарт-групп
У оповещений в Azure Monitor теперь есть [состояние предупреждения и условие мониторинга](https://aka.ms/azure-alerts-overview), и аналогично у смарт-групп есть [состояние смарт-группы](https://aka.ms/smart-groups). Изменения в состоянии теперь записываются в журнал, связанный с соответствующим оповещением или смарт-группой. В этой статье описывается процесс изменения состояния как для оповещения, так и для смарт-группы.

## <a name="change-the-state-of-an-alert"></a>Изменение состояния оповещения
1. Состояние оповещения можно изменить различными способами. 
    * На странице "Все оповещения" установите флажки напротив оповещений, для которых вы хотите изменить состояние, и щелкните "Изменить состояние".   
    ![Мониторинг](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * На странице "Сведения об оповещениях" конкретного экземпляра оповещения можно щелкнуть "Изменить состояние".   
    ![Мониторинг](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * На странице "Сведения об оповещениях" конкретного экземпляра оповещения в области смарт-групп можно установить флажок напротив нужных оповещений.    
    ![Мониторинг](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * На странице "Сведения о смарт-группах" в списке оповещений участников можно установить флажки напротив оповещений, для которых вы хотите изменить состояние, и щелкнуть "Изменить состояние".   
    ![Мониторинг](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Щелкнув "Изменить состояние", вы увидите контекстное меню, в котором можно выбрать состояние (Новое/Подтверждено/Закрытое) и при необходимости ввести комментарий.   
![Мониторинг](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. После этого изменение состояния записывается в журнал соответствующего оповещения. Его можно просмотреть, открыв соответствующую страницу сведений и проверив раздел журнала.    
![Мониторинг](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Изменение состояния смарт-группы
1. Состояние смарт-группы можно изменить различными способами.
    1. На странице списка смарт-группы установите флажки напротив смарт-групп, для которых вы хотите изменить состояние, и щелкните "Изменить состояние".  
    ![Мониторинг](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Можно щелкнуть "Изменить состояние" на странице сведений о смарт-группах        
    ![Мониторинг](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Щелкнув "Изменить состояние", вы увидите контекстное меню, в котором можно выбрать состояние (Новое/Подтверждено/Закрытое) и при необходимости ввести комментарий. 
![Мониторинг](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  При изменении состояния смарт-группы состояние отдельных оповещений участников не будет изменено.

1. После этого изменение состояния записывается в журнал соответствующей смарт-группы. Его можно просмотреть, открыв соответствующую страницу сведений и проверив раздел журнала.     
![Мониторинг](./media/alerts-managing-alert-states/state-sg-history.jpg)

