---
title: Пример скрипта Azure CLI. Создание кластера Kubernetes Windows ACS
description: Пример скрипта Azure CLI. Создание кластера Kubernetes Windows ACS
author: iainfoulds
tags: acs, azure-container-service
keywords: Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: bc940f09a98eb4ee42290dcfd11d0800f6c3b9e4
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76270673"
---
# <a name="deprecated-create-an-azure-container-service-kubernetes-windows-cluster"></a>Создание кластера Kubernetes Windows службы контейнеров Azure (не рекомендуется)

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

В этом примере создается кластер службы контейнеров Azure для запуска Kubernetes для контейнеров на основе Windows.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys \
  --admin-username azureuser \
  --admin-password Password12 \
  --windows
```

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az-acs-create) | Создает кластер ACS. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов CLI службы контейнеров Azure см. в [документации по службе контейнеров Azure](../cli-samples.md).
