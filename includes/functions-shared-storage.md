---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963667"
---
Чтобы повысить производительность, используйте отдельную учетную запись хранения для каждого приложения-функции. Это особенно важно, если у вас есть Устойчивые функции или функции, активируемые концентратором событий, которые создают большой объем транзакций хранилища. Если логика приложения взаимодействует с хранилищем Azure напрямую (с помощью пакета SDK хранилища) или с помощью одной из привязок к хранилищу, следует использовать выделенную учетную запись хранения. Например, если у вас есть функция, активируемая концентратором событий, которая записывает данные в хранилище BLOB-объектов, используйте две учетные записи хранения&mdash;одну для приложения-функции, а другую для больших двоичных объектов, хранимых функцией.