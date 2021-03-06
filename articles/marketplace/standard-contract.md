---
title: Стандартный контракт | Службы
description: Стандартный контракт для Azure Marketplace и AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/19/2019
ms.author: v-chjen
ms.openlocfilehash: dc8edf2b6e4a1204e7edd515da9323896049eb13
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228212"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Стандартный контракт для коммерческого рынка Майкрософт

Чтобы упростить процесс приобретения клиентов и снизить юридическую сложность для поставщиков программного обеспечения, корпорация Майкрософт предлагает стандартный контракт для коммерческого рынка Майкрософт, который поможет упростить транзакции в Marketplace. Вместо того чтобы создавать собственные условия, издатели коммерческих магазинов могут предлагать программное обеспечение в соответствии со стандартным контрактом, который клиенты должны венесуэльскому и принимать только один раз. Стандартный контракт можно найти здесь: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Условия предложения определяются при создании предложения в центре партнеров или в Портал Cloud Partner. Вы можете использовать стандартный контракт для коммерческого рынка Майкрософт вместо того, чтобы предоставлять собственные пользовательские условия.

>[!Note]
>После публикации предложения с помощью стандартного контракта для коммерческого рынка Майкрософт вы не сможете использовать собственные условия. Это сценарий «или». Вы либо предлагаете свое решение в соответствии со стандартным контрактом, *либо* ваши собственные условия. Если вы хотите изменить условия стандартного контракта, это можно сделать с помощью стандартных поправок контракта.

## <a name="standard-contract-amendments"></a>Поправки стандартного контракта

Поправка стандартного контракта позволяет издателям выбрать стандартный контракт для простоты и настроить условия для их продукта или бизнеса. Клиентам необходимо только просмотреть внесенные в контракте поправки, если они уже проверили и приняли Соглашение Microsoft Standard.

Для коммерческих издателей Marketplace доступно два вида поправности:

* Универсальные поправки. Эти действия применяются к стандартному контракту для всех клиентов. Универсальные поправки отображаются для каждого клиента предложения в потоке покупки. Клиенты должны принять условия стандартного контракта и поправки, прежде чем они смогут использовать ваше предложение.

* Пользовательские поправки. Эти действия являются специальными для стандартных контрактов, которые предназначены для конкретных клиентов только через идентификаторы клиентов Azure. Издатели могут выбрать целевой клиент. Только клиенты из клиента будут представлены с условиями пользовательской переправки в потоке покупки предложения.  Клиенты должны принять условия стандартного контракта и поправки, прежде чем они смогут использовать ваше предложение.

>[!Note]
>Эти два типа стека поправки поверх других. Клиенты, нацеленные на пользовательскую поправку, также получат универсальную поправку на стандартный контракт во время покупки.

Вы можете использовать стандартный контракт для коммерческого рынка Майкрософт для следующих типов предложений: приложения Azure (шаблоны решений и управляемые приложения), виртуальные машины, контейнеры, приложения-контейнеры, IoT Edge модули и SaaS. .

## <a name="customer-experience"></a>Взаимодействие с пользователем

Во время обнаружения в Azure Marketplace или AppSource клиенты смогут просматривать термины, связанные с предложением, как стандартный контракт для коммерческого рынка Майкрософт и любые универсальные поправки.

![Портал Azureный процесс обнаружения клиентов.](media/marketplace-publishers-guide/azure-discovery-process.png)

В ходе процесса покупки в портал Azure клиенты смогут видеть условия, связанные с предложением, как стандартный контракт для коммерческого рынка Майкрософт и любые специальные и/или зависящие от клиента поправки.

![Портал Azure покупатель.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Клиенты могут использовать Get-AzureRmMarketplaceTerms, чтобы получить условия предложения и принять его. Стандартный контракт и связанные с ним поправки будут возвращены в выходных данных командлета.
