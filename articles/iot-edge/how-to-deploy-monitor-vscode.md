---
title: Развертывание модулей в масштабе с помощью Visual Studio Code Azure IoT Edge
description: Используйте расширение Интернета вещей для Visual Studio Code, чтобы создать автоматические развертывания для групп устройств IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774137"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Развертывание модулей IoT Edge в масштабе с помощью Visual Studio Code

Вы можете создать **IOT Edge автоматическое развертывание** с помощью Visual Studio Code, чтобы управлять текущими развертываниями для нескольких устройств одновременно. Автоматические развертывания для IoT Edge входят в функцию [автоматического управления устройствами](/azure/iot-hub/iot-hub-automatic-device-management) в центре Интернета вещей. Развертывания — это динамические процессы, которые позволяют развертывать несколько модулей на нескольких устройствах. Кроме того, можно отвести наблюдение за состоянием и работоспособностью модулей, а также при необходимости вносить изменения.

Дополнительные сведения см. в статье [IOT Edge автоматическое развертывание для отдельных устройств или в масштабе](module-deployment-monitoring.md).

В этой статье вы настроили Visual Studio Code и расширение Интернета вещей. Затем вы узнаете, как развертывать модули в наборе IoT Edge устройств.

## <a name="prerequisites"></a>Технические условия

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.
* [Устройство IoT Edge](how-to-register-device.md#register-with-visual-studio-code) с установленной средой выполнения IoT Edge.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Средства Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) для Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Вход в систему для получения доступа к Центру Интернета вещей

Вы можете использовать расширения Azure IoT для Visual Studio Code выполнения операций с центром. Чтобы эти операции работали, необходимо войти в учетную запись Azure и выбрать центр Интернета вещей, с которым вы работаете.

1. Откройте в Visual Studio Code представление **Explorer**.

1. В нижней части обозревателя разверните раздел **центр Интернета вещей Azure** .

1. Щелкните.. **.** в заголовке раздела центра **Интернета вещей Azure** . Если значок многоточия не отображается, наведите курсор на заголовок.

1. Щелкните **Выбрать Центр Интернета вещей**.

1. Если вы не вошли в учетную запись Azure, следуйте инструкциям на экране.

1. Выберите подписку Azure.

1. Выберите нужный Центр Интернета вещей.

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, описывающий, какие модули следует развернуть. Здесь также описывается, как потоки данных между модулями и требуемые свойства модуля двойников. Дополнительные сведения см. в статьях о [развертывании модулей и установке маршрутов в IOT Edge](module-composition.md).

Чтобы развернуть модули с помощью Visual Studio Code, сохраните манифест развертывания на локальный компьютер в формате JSON. Необходимо указать расположение при выполнении команды, чтобы применить конфигурацию к устройству.

Вот пример простого манифеста развертывания с одним модулем.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Если необходимо определить, какие IoT Edge устройства можно настроить в настоящее время, выполните команду **IOT Edge: получить сведения об устройстве** .

## <a name="identify-devices-with-target-conditions"></a>Определение устройств с целевыми условиями

Чтобы определить устройства IoT Edge, которые должны получить развертывание, необходимо указать целевое условие. Целевое условие выполняется, если указанные критерии соответствуют идентификатору deviceId, значению тега или значению сообщаемого свойства.

Теги настраиваются в двойникае устройства. Ниже приведен пример двойникаа устройства с тегами:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Это устройство будет принимать развертывание, если целевое условие развертывания содержит выражение, совпадающее с одним из значений тега, например `tag.location.building = '20'`.

Если вы хотите ориентироваться на конкретное устройство независимо от его тегов или других значений, просто укажите `deviceId` для целевого условия.

Ниже приведены некоторые дополнительные примеры.

* deviceId ='linuxprod1'
* deviceId = ' linuxprod1 ' или deviceId = ' linuxprod2 ' или deviceId = ' linuxprod3 '
* tags.environment ='prod'
* Tags. Environment = ' произв. ' и Tags. location = ' westus2 '
* Tags. Environment = ' произв. ' или Tags. location = ' westus2 '
* Tags. operator = ' John ' и Tags. Environment = ' "произв. ' ', а не deviceId = ' linuxprod1 '

Дополнительные сведения см. в описании [целевого условия](module-deployment-monitoring.md#target-condition) . Дополнительные сведения о двойниках устройств и тегах см. в статье [Общие сведения о двойниках устройств и их использование в Центре Интернета вещей](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Изменение двойникаа устройства

Вы можете изменить двойника устройства в Visual Studio Code, чтобы настроить теги. В меню **вид** выберите пункт **Палитра команд** и выполните команду **IOT Edge: Edit Device двойника** . Выберите устройство IoT Edge и появится двойника устройства.

В этом примере теги не определены. Замените текущий пустой раздел `"tags": {}` собственным определением тегов.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

После сохранения локального файла выполните команду **IOT Edge: Update Device двойника** .

## <a name="create-deployment-at-scale"></a>Создание развертывания в масштабе

После настройки манифеста развертывания и настроенных тегов в двойникае устройства можно приступать к развертыванию.

1. В меню **вид** выберите пункт **Палитра команд** и выберите команду **Azure IOT Edge: создать развертывание в масштабе** .

1. Перейдите к файлу JSON манифеста развертывания, который необходимо использовать, и нажмите **Выберите манифест развертывания Edge**.

1. При появлении запроса укажите значения, начиная с **идентификатора развертывания**.

   ![Укажите идентификатор развертывания](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Укажите значения для этих параметров:

  | Параметр | Description |
  | --- | --- |
  | Идентификатор развертывания | Имя развертывания, которое будет создано в центре Интернета вещей. Присвойте своему развертыванию уникальное имя, содержащее до 128 букв в нижнем регистре. Не используйте пробелы и следующие недопустимые символы: `& ^ [ ] { } \ | " < > /`. |
  | Условие назначения | Введите целевое условие, чтобы определить, какие устройства будут направлены в это развертывание. Условие основано на тегах двойникаа устройства или сообщаемых свойствах двойникаа устройства и должно соответствовать формату выражения. Например, `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Приоритет |  Положительное целое число. Если на одном устройстве нацелено два или более развертывания, будет применяться развертывание с наибольшим числовым значением приоритета. |

  После указания приоритета терминал должен отобразить выходные данные, аналогичные приведенным ниже.

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Мониторинг и изменение развертываний

Используйте [Azure CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment) или [портал Azure](how-to-deploy-monitor.md#monitor-a-deployment) для мониторинга, изменения и удаления развертываний. Оба предоставляют метрики для развертываний.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [развертывании модулей на IOT Edge устройствах](module-deployment-monitoring.md).
