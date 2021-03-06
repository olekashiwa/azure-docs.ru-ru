---
title: Что такое IP-адрес 168.63.129.16? | Документы Майкрософт
description: Дополнительные сведения об IP-адресе 168.63.129.16 и о том, как он работает с ресурсами.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114358"
---
# <a name="what-is-ip-address-1686312916"></a>Что такое IP-адрес 168.63.129.16?

IP-адрес 168.63.129.16 является виртуальным общедоступным IP-адресом, который используется для содействия коммуникационному каналу, установленному с ресурсами платформы Azure. Клиенты могут определять любое адресное пространство для своей частной виртуальной сети в Azure. Таким образом, ресурсы платформы Azure должны быть представлены как уникальный общедоступный IP-адрес. Этот виртуальный общедоступный IP-адрес разрешает:

- агенту виртуальной машины взаимодействовать с платформой Azure, чтобы сигнализировать, что он "Готов";
- связь с виртуальным сервером DNS для предоставления разрешения отфильтрованных имен для ресурсов (например, для виртуальной машины), которые не имеют настраиваемого DNS-сервера. Такая фильтрация гарантирует, что клиенты могут разрешать только имена узлов своих ресурсов;
- Включает [пробы работоспособности из балансировщика нагрузки Azure](../load-balancer/load-balancer-custom-probe-overview.md) для определения состояния работоспособности виртуальных машин.
- Позволяет виртуальной машине получить динамический IP-адрес от службы DHCP в Azure.
- сообщения пульса гостевого агента для роли PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Область действия IP-адреса 168.63.129.16

Общедоступный IP-адрес 168.63.129.16 используется во всех регионах и в национальных облаках. Этот Специальный общедоступный IP-адрес принадлежит корпорации Майкрософт и не изменится. Рекомендуется разрешить этот IP-адрес в любых локальных (на виртуальной машине) политиках брандмауэра (исходящее направление). Обмен данными между этим специальным IP-адресом и ресурсами безопасный, так как только внутренняя платформа Azure может быть источником сообщения с этого IP-адреса. Если этот адрес будет заблокирован, может возникнуть непредвиденная реакция в различных сценариях. 168.63.129.16 — это [виртуальный IP-адрес узла узла](../virtual-network/security-overview.md#azure-platform-considerations) , поэтому он не подчиняется определяемым пользователем маршрутам.

- Агенту виртуальной машины требуется Исходящая связь через порты 80, 443, 32526 с Виресервер (168.63.129.16). Они должны быть открыты в локальном брандмауэре на виртуальной машине. Обмен данными через эти порты с 168.63.129.16 не зависит от настроенных групп безопасности сети.
- 168.63.129.16 может предоставлять службы DNS для виртуальной машины. Если это не требуется, этот трафик можно заблокировать в локальном брандмауэре на виртуальной машине. По умолчанию обмен данными с DNS не подчиняется настроенным группам безопасности сети, если специально не используется тег службы [азуреплатформднс](../virtual-network/service-tags-overview.md#available-service-tags) .
- Если виртуальная машина входит в внутренний пул подсистемы балансировки нагрузки, необходимо разрешить обмен данными [проверки работоспособности](../load-balancer/load-balancer-custom-probe-overview.md) из 168.63.129.16. Конфигурация группы безопасности сети по умолчанию имеет правило, разрешающее это взаимодействие. Это правило использует тег службы [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) . При необходимости этот трафик можно заблокировать, настроив группу безопасности сети, однако это приведет к сбою проб.

В сценарии, не использующем виртуальную сеть (классическая модель), проверка работоспособности источника выполняется из частного IP-адреса и 168.63.129.16 не используется.

## <a name="next-steps"></a>Дальнейшие действия

- [Группы безопасности](security-overview.md)
- [Создание, изменение и удаление группы безопасности сети](manage-network-security-group.md)
