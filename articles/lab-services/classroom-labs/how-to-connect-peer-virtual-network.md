---
title: Подключение к одноранговой сети в службах лаборатории Azure | Документация Майкрософт
description: Узнайте, как подключить лабораторную сеть к другой сети в качестве однорангового. Например, подключите локальную школьную или университетскую сеть к виртуальной сети лаборатории в Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: d3f6acef7491a07f94eec0b2c3b2f3bcd9c01a33
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701669"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Подключение сети лаборатории к одноранговой виртуальной сети в службах лаборатории Azure 
Эта статья содержит сведения о пиринга сети Labs с другой сетью. 

## <a name="overview"></a>Краткое описание
Пиринг виртуальных сетей позволяет легко подключать виртуальные сети Azure. После создания пиринговой связи две виртуальные сети выглядят как одна сеть при подключении. Трафик между виртуальными машинами в одноранговых виртуальных сетях направляется через магистральную инфраструктуру Майкрософт, так же как трафик направляется между виртуальными машинами в одной виртуальной сети по только частным IP-адресам. Дополнительные сведения см. в статье [Пиринг между виртуальными сетями](../../virtual-network/virtual-network-peering-overview.md).

Возможно, потребуется подключить сеть лаборатории к одноранговой виртуальной сети в некоторых сценариях, включая следующие:

- Виртуальные машины в лаборатории имеют программное обеспечение, которое подключается к локальным серверам лицензий для получения лицензии.
- Виртуальным машинам в лаборатории требуется доступ к наборам данных (или любым другим файлам) в общих сетевых ресурсах университета. 

Некоторые локальные сети подключены к виртуальной сети Azure либо через [ExpressRoute](../../expressroute/expressroute-introduction.md) , либо через [шлюз виртуальной сети](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Эти службы должны быть настроены за пределами служб лаборатории Azure. Дополнительные сведения о подключении локальной сети к Azure с помощью ExpressRoute см. в [обзоре expressroute](../../expressroute/expressroute-introduction.md). Для локального подключения с помощью шлюза виртуальной сети шлюз, указанная виртуальная сеть и учетная запись лаборатории должны находиться в одном регионе.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Настройка во время создания учетной записи лаборатории
Во время создания учетной записи лаборатории можно выбрать существующую виртуальную сеть, которая отображается в раскрывающемся списке **одноранговая виртуальная сеть** . Выбранная виртуальная сеть подключена к лабораториям, созданным в учетной записи лаборатории. Все виртуальные машины в лабораториях, созданные после внесения этого изменения, будут иметь доступ к ресурсам в одноранговой виртуальной сети. 

![Выберите виртуальную сеть для одноранговой](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Подробные пошаговые инструкции по созданию учетной записи лаборатории см. в разделе [Настройка учетной записи лаборатории](tutorial-setup-lab-account.md) .


## <a name="configure-after-the-lab-is-created"></a>Настройка после создания лаборатории
Это же свойство можно включить на вкладке **Конфигурация лабораторий** на странице **учетная запись лаборатории** , если вы не настроили одноранговую сеть во время создания учетной записи лаборатории. Изменения, внесенные в этот параметр, применяются только к лабораториям, созданным после изменения. Как видно на рисунке, вы можете включить или отключить **одноранговую виртуальную сеть** для лабораторий в учетной записи лаборатории. 

![Включение или отключение пиринга виртуальной сети после создания лаборатории](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Если выбрана виртуальная сеть для поля **одноранговая виртуальная сеть** , параметр **Разрешить создателю лаборатории выбрать расположение лаборатории** будет отключен. Это связано с тем, что лаборатории в учетной записи лаборатории должны находиться в том же регионе, что и учетная запись лаборатории для подключения к ресурсам в одноранговой виртуальной сети. 

> [!IMPORTANT]
> Изменение этого параметра применимо только к лабораториям, созданным после внесения изменений, а не к существующим лабораториям. 


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [Создание и администрирование учетных записей лаборатории (для администраторов)](how-to-manage-lab-accounts.md)
- [Создание и администрирование учетных записей лаборатории (для владельцев лаборатории)](how-to-manage-classroom-labs.md)
- [Настройка и публикация шаблонов (для владельцев лаборатории)](how-to-create-manage-template.md)
- [Доступ к лабораториям для аудитории (для пользователей лаборатории)](how-to-use-classroom-lab.md)

