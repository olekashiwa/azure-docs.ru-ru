---
title: Создание центра Интернета вещей Azure с помощью шаблона (PowerShell) | Документация Майкрософт
description: Использование шаблона Azure Resource Manager для создания центра Интернета вещей с Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976621"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Узнайте, как использовать шаблон Azure Resource Manager для создания центра Интернета вещей и группы потребителей. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Дополнительные сведения о разработке шаблонов диспетчер ресурсов см. в [документации по Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Шаблон диспетчер ресурсов, используемый в этом кратком руководстве, относится к [шаблонам](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/)быстрого запуска Azure. Ниже приведена копия шаблона:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Шаблон создает центр Интернета вещей Azure с тремя конечными точками (eventhub, Облако-устройство и обмен сообщениями) и группой потребителей. Дополнительные примеры шаблонов см. в статье шаблоны быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Схему шаблона центра Интернета вещей можно найти [здесь](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Существует несколько способов развертывания шаблона.  В этом руководстве используется Azure PowerShell.

Чтобы запустить сценарий PowerShell, выберите " **попробовать** ", чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду Вставить:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Как видно из сценария PowerShell, шаблон используется в шаблонах быстрого запуска Azure. Чтобы использовать собственные файлы, необходимо сначала отправить файл шаблона в Cloud Shell, а затем использовать параметр `-TemplateFile`, чтобы указать имя файла.  Пример см. в разделе [Развертывание шаблона](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда центр Интернета вещей развернут с помощью шаблона Azure Resource Manager, может потребоваться дальнейшее изучение:

* Узнайте о возможностях [поставщика ресурсов центра Интернета вещей REST API][lnk-rest-api].
* Дополнительные сведения о возможностях Azure Resource Manager см. в статье [Azure Resource Manager обзор][lnk-azure-rm-overview] .
* Синтаксис JSON и используемые в шаблоне свойства см. в статье о [типах ресурсов Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Пакет SDK для устройств Azure IoT для C][lnk-c-sdk]
* [Пакеты SDK для Центра Интернета вещей Azure][lnk-sdks]

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
