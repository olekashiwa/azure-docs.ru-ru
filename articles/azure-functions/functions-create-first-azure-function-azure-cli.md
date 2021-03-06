---
title: Создание первой функции с помощью Azure CLI
description: Узнайте, как создать свою первую Функцию Azure для бессерверного выполнения с помощью Azure CLI и основных инструментов службы "Функции Azure".
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 222e4a98974a1af40ff860cfc4fdb246d9c97bca
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769393"
---
# <a name="quickstart-create-your-first-function-from-the-command-line-using-azure-cli"></a>Краткое руководство. Создание первой функции из командной строки с помощью Azure CLI

В этом кратком руководстве рассматривается создание первой функции из командной строки или терминала. Используйте Azure CLI для создания приложения-функции, которое является [независимой от сервера](https://azure.microsoft.com/solutions/serverless/) инфраструктурой, где размещается ваша функция. Проект кода функции создается из шаблона с помощью [основных инструментов службы "Функции Azure"](functions-run-local.md), которые также используются для развертывания проекта приложения-функции в Azure.

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux.

## <a name="prerequisites"></a>предварительные требования

Перед выполнением этого примера вам понадобится следующее:

+ установить [Azure Functions Core Tools](./functions-run-local.md#v2) версии 2.6.666 или более поздней;

+ Установка [Azure CLI](/cli/azure/install-azure-cli). В этой статье требуется Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Можно также использовать [Azure Cloud Shell](https://shell.azure.com/bash).

+ Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Включение пакетов расширений

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, предоставляющее среду для выполнения кода функции без сервера. Это позволяет группировать функции в логические единицы, чтобы упростить управление, развертывание, масштабирование и совместное использование ресурсов. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

В следующей команде замените `<APP_NAME>` уникальным именем вашего приложения функции, а `<STORAGE_NAME>` — именем учетной записи хранения. `<APP_NAME>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. Также следует задать среду выполнения `<language>` для приложения-функции из `dotnet` (C#) или `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

Если задать параметр _consumption-plan-location_, приложение-функция размещено в плане размещения по мере использования. В этом бессерверном плане ресурсы добавляются динамически, когда этого требуют функции, и оплачивается только выполнение функций. Дополнительные сведения см. в статье [Выбор правильного плана обслуживания для Функций Azure](functions-scale.md).

После создания приложения-функции в Azure CLI отображаются следующие сведения:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

