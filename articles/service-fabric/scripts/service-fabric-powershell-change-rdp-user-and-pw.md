---
title: Обновление имени пользователя и пароля для входа по протоколу RDP в PowerShell
description: Пример сценария Azure PowerShell для обновления имени пользователя и пароля RDP для всех узлов кластера Service Fabric, относящихся к конкретному типа узла.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 0cf9f8362d3fa683084aef840cf35cd9a843608a
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614814"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Обновление имени пользователя и пароля администратора виртуальных машин в кластере

Масштабируемый набор виртуальных машин соответствует [типу узла](../service-fabric-cluster-nodetypes.md) в кластере Service Fabric. Этот сценарий обновляет имя пользователя и пароль администратора для виртуальных машин кластера на узле определенного типа.  Добавьте расширение VMAccessAgent в масштабируемый набор, так как пароль администратора является неизменяемым свойством масштабируемого набора.  Изменение имени пользователя и пароля применяется ко всем узлам в масштабируемом наборе. Измените параметры, если это необходимо.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Получает свойства типа узла кластера (масштабируемого набора виртуальных машин).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Добавляет расширение в масштабируемый набор виртуальных машин.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Обновляет состояние масштабируемого набора виртуальных машин до состояния локального объекта VMSS.|

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).
