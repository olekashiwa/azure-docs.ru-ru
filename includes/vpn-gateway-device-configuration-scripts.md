---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111199"
---
| **поставщик** | **Семейство устройств** | **Версия встроенного ПО** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (предварительная версия)|
|Cisco | ASA | ASA* RouteBased (IKEv2 — без BGP) для ASA до версии 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 — без BGP) для ASA 9.8+ |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| Routebased v1.10x EdgeOS VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> ( * ) Обязательный параметр: Нарровазуретраффикселекторс (включение параметра UsePolicyBasedTrafficSelectors) и КустомазуреполиЦиес (IKE/IPsec)
>
