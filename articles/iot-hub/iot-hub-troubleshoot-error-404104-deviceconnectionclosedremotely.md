---
title: Устранение неполадок центра Интернета вещей Azure 404104 Девицеконнектионклоседремотели
description: Сведения об исправлении ошибки 404104 Девицеконнектионклоседремотели
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 686946b65b4b7540f404a291c87c5ad9c7b7a0ca
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960806"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

В этой статье описываются причины и решения для ошибок **404104 девицеконнектионклоседремотели** .

## <a name="symptoms"></a>Симптомы

### <a name="symptom-1"></a>Симптом 1

Устройства отключаются через равные промежутки времени (например, каждые 65 минут), и вы увидите **404104 девицеконнектионклоседремотели** в журналах диагностики центра Интернета вещей. Иногда вы также видите **401003 иосубунаусоризед** и событие успешного подключения устройства меньше минуты.

### <a name="symptom-2"></a>Симптом 2

Устройства отключаются случайным образом, и вы видите **404104 девицеконнектионклоседремотели** в журналах диагностики центра Интернета вещей.

### <a name="symptom-3"></a>Симптом 3

Многие устройства отключаются одновременно, вы видите DIP-адрес в [метрике подключенных устройств](iot-hub-metrics.md)и в журналах диагностики больше **404104 Девицеконнектионклоседремотели** и [500xxx внутренних ошибок](iot-hub-troubleshoot-error-500xxx-internal-errors.md) , чем обычно.

## <a name="causes"></a>Причины

### <a name="cause-1"></a>Причина 1

Истек срок действия [маркера SAS, используемого для подключения к центру Интернета вещей](iot-hub-devguide-security.md#security-tokens) . в результате центр Интернета вещей отключится от устройства. Соединение будет восстановлено при обновлении маркера устройством. Например, [срок действия маркера SAS истекает каждый час по умолчанию для пакета SDK C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), что может привести к обычному отключению.

Дополнительные сведения см. в разделе [401003 иосубунаусоризед причина](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Причина 2

К некоторым возможностям относятся:

- Устройство потеряло базовое сетевое подключение дольше, чем [MQTT поддержания активности](iot-hub-mqtt-support.md#default-keep-alive-timeout), что приводит к удаленному времени ожидания простоя. Параметр проверки активности MQTT может быть разным для каждого устройства.

- Устройство отправило сброс на уровне TCP/IP, но не отправило `MQTT DISCONNECT`уровня приложения. По сути, устройство внезапно закрыло базовое подключение к сокету. Иногда эта проблема вызвана ошибками в более ранних версиях пакета SDK для IoT Azure.

- Сбой приложения на стороне устройства.

### <a name="cause-3"></a>Причина 3

В центре Интернета вещей может возникать временная проблема. См. статью [ошибка внутреннего сервера центра Интернета вещей](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Решения

### <a name="solution-1"></a>Решение 1

См. [401003 иосубунаусоризед решение 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Решение 2

- Убедитесь, что устройство имеет хорошее подключение к центру Интернета вещей, [проверив подключение](tutorial-connectivity.md). Если сеть является ненадежной или временной, мы не рекомендуем увеличивать значение проверки активности, так как это может привести к выпуску обнаружения (например, через Azure Monitor предупреждения) дольше. 

- Используйте последние версии [пакетов SDK для IOT](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Решение 3

См. раздел [решения для внутренних ошибок сервера центра Интернета вещей](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Дальнейшие действия

Для надежного управления подключениями рекомендуется использовать пакеты SDK для устройств Azure IoT. Дополнительные сведения см. в статье [Управление подключением и надежным обменом сообщениями с помощью пакетов SDK для устройств центра Интернета вещей Azure](iot-hub-reliability-features-in-sdks.md) .