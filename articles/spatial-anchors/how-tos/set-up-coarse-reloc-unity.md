---
title: Простое уточнение расположения в Unity
description: В этой статье подробно объясняется, как создавать и находить привязки с использованием простого уточнения расположения на C#.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5c976bd020d37672c44c89113bf7786e1ccf141b
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548260"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-c"></a>Создание и поиск привязок с использованием простого уточнения расположения на C#

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android (Java)](set-up-coarse-reloc-java.md)
> * [C++ (NDK)](set-up-coarse-reloc-cpp-ndk.md)
> * [C++ (WinRT)](set-up-coarse-reloc-cpp-winrt.md)

Пространственные привязки Azure могут связывать данные датчика размещения на устройстве с созданными вами привязками. С помощью этих данных также можно быстро определить, есть ли привязки рядом с устройством. Дополнительные сведения см. в статье [Coarse relocalization](../concepts/coarse-reloc.md) (Простое уточнение расположения).

## <a name="prerequisites"></a>Предварительные требования

Для работы с руководством вам потребуется следующее:

- Базовое знание C#.
- Прочесть статью [Описание службы "Пространственные привязки Azure"](../overview.md).
- Выполнить одно из [5-минутных руководств](../index.yml).
- Ознакомиться с инструкциями в статье [Создание и поиск привязок с помощью Пространственных привязок Azure](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```csharp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Allow GPS
sensorProvider.Sensors.GeoLocationEnabled = true;

// Allow WiFi scanning
sensorProvider.Sensors.WifiEnabled = true;

// Allow a set of known BLE beacons
sensorProvider.Sensors.BluetoothEnabled = true;
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```csharp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters = 5;
nearDeviceCriteria.MaxResultCount = 25;

// Set the session's locate criteria
AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]