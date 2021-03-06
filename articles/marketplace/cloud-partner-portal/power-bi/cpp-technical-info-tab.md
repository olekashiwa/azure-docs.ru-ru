---
title: Технические сведения о предложении для Power BI приложения | Azure Marketplace
description: Настройка полей технической информации для предложений приложений Power BI для Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 02c98e38f26fff73d02dc4c2b838ad3428cd2996
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819032"
---
# <a name="power-bi-apps-technical-info-tab"></a>Вкладка технической информации о приложениях Power BI

На странице **новое предложение** используйте вкладку **технические сведения** , чтобы указать URL-адрес пакета установщика Power BI и другие сведения, необходимые для проверки нового предложения.  В первоначальном выпуске все Power BI приложения доступны бесплатно и доступны для скачивания из AppSource. По этой причине нельзя определить единицы складского учета (SKU) для этого типа предложения.

![Вкладка "технические сведения"](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Поля на вкладке с техническими сведениями 

На вкладке **технические сведения** заполните поля, описанные в следующей таблице. Звездочка (*) в конце метки поля означает, что поле является обязательным.

|        Поле          |  Description (Описание)                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL-адрес установщика\***     | Power BI создает этот URL-адрес при публикации приложения и его повышении до рабочей среды.  Дополнительные сведения см. [в разделе Публикация приложений с панелями мониторинга и отчетами в Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Validation instructions** (Инструкции по проверке)  |  При необходимости добавьте инструкции (до 3 000 символов), чтобы помочь команде Microsoft Validation настроить, подключить и протестировать приложение. Включите стандартные параметры конфигурации, учетные записи, параметры или другие сведения, которые можно использовать для проверки параметра Connect data. Эта информация видна только команде проверки и используется только в целях проверки.  |
| **Is this app created as a Power BI content pack?** (Создается ли это приложение в виде пакета содержимого Power BI?) | В настоящее время это поле используется только для внутренних целей. Оставьте значение по умолчанию — **нет**. Если изменить значение параметра на **Да**, процесс публикации можно будет прерывать.  |  
|  |  |


## <a name="next-steps"></a>Дальнейшие действия

На вкладке [сведения о витрине](./cpp-storefront-details-tab.md) укажите маркетинговую и юридическую информацию для своего приложения.

