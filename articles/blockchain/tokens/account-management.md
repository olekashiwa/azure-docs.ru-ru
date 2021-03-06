---
title: Управление учетными записями маркеров Azure Блокчейн
description: С помощью управления учетными записями маркеров Azure Блокчейн можно создавать группы и связывать учетные записи блокчейн для управления доступом к действиям блокчейн.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326099"
---
# <a name="azure-blockchain-tokens-account-management"></a>Управление учетными записями маркеров Azure Блокчейн

[!INCLUDE [Preview note](./includes/preview.md)]

Для решения блокчейн пользователям могут потребоваться разные уровни доступа к маркерам, созданным с помощью службы маркеров Блокчейн Azure. В большинстве случаев блокчейн необходимо спланировать и развернуть разные учетные записи блокчейн, которые существуют в главной книге. Кроме того, необходимо управлять доступом между участниками. С помощью управления учетными записями маркеров Azure Блокчейн можно создавать группы и связывать учетные записи блокчейн для управления доступом к действиям блокчейн.

## <a name="blockchain-networks"></a>Блокчейн сети

Маркеры Azure Блокчейн позволяют развертывать маркеры и управлять ими в наборе блокчейн сетей. К службе можно подключить одну книгу блокчейн или несколько книг блокчейн.

## <a name="accounts"></a>Учетные записи

Для сетей блокчейн, подключенных к маркерам Блокчейн для Azure, служба создает и управляет парой закрытых открытых ключей учетной записи и выполняет подписывание и отправку транзакций. Маркеры Блокчейн Azure также предоставляют Сопоставление удостоверений для сопоставления учетных записей с удостоверением открытого ключа в главной книге.

## <a name="groups"></a>Группы

Группы позволяют управлять большим количеством учетных записей блокчейн в подключенных сетях. Вы можете отслеживать и проверять, какие приложения и пользователи в каталоге имеют возможность использовать учетные записи через API маркеров Azure Блокчейн. Например, можно сгруппировать набор учетных записей, представляющих различные строки бизнеса или различные роли, и доступ к маркерам блокчейн.

Можно также связать группу с Azure Active Directory пользователем или субъектом-службой, и этот субъект имеет разрешения для группы и связанных с ней учетных записей.  

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о [шаблонах службы "Токены блокчейна Azure"](templates.md).
