---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185139"
---
1. Войдите в свою подписку Azure с помощью команды [az login](/cli/azure/) и следуйте инструкциям на экране. Дополнительные сведения о входе в систему см. в статье [Начало работы с Azure CLI](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Если у вас есть несколько подписок Azure, укажите подписки для этой учетной записи.

   ```azurecli
   az account list --all
   ```
3. Укажите подписку, которую нужно использовать.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
