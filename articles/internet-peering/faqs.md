---
title: Интернет-пиринг — часто задаваемые вопросы
titleSuffix: Azure
description: Интернет-пиринг — часто задаваемые вопросы
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775502"
---
# <a name="internet-peering---faqs"></a>Интернет-пиринг — часто задаваемые вопросы

Для получения общих вопросов можно ознакомиться со сведениями ниже.

**В чем разница между пирингом Интернета и службой пиринга?**

Служба пиринга — это служба, которая планирует предоставлять корпоративным клиентам возможность подключения к корпорации Майкрософт по общедоступному IP-адресу. В Интернете корпоративного уровня есть возможность подключения через поставщиков услуг Интернета с высокой пропускной способностью, обеспечивающей подключение к Майкрософт и избыточность для обеспечения высокой доступности. Кроме того, пользовательский трафик оптимизирован для задержки до ближайшего Microsoft ребра. Служба пиринга выполняет сборку на одноранговом подключении с перевозчиком партнера. Подключение пиринга с партнером должно быть прямым пиринг, а не пиринг Exchange. Прямой пиринг должен иметь локальную и геоизбыточность.

**Что такое устаревший пиринг?**

Подключение пиринга, настроенное с помощью Azure PowerShell, управляется как ресурс Azure. Одноранговые подключения, настроенные в прошлом, хранятся в нашей системе как устаревшие пиринга, которые можно преобразовать для управления как ресурс Azure.

**Какие IP-адреса предоставляются корпорации Майкрософт и одноранговым устройствам при вызове New-Азпирингдиректконнектионобжект?**

При вызове командлета New-Азпирингдиректконнектионобжект введены адрес/31 (a. b. c. d/31) или/30 адрес (a. b. c. d/30). Первый IP-адрес (a. b. c. d + 0) передается устройству однорангового устройства, а второй IP-адрес (a. b. c. d + 1) предоставляется устройству Майкрософт.

**Что такое параметры MaxPrefixesAdvertisedIPv4 и MaxPrefixesAdvertisedIPv6 в командлете New-Азпирингдиректконнектионобжект?**

Параметры MaxPrefixesAdvertisedIPv4 и MaxPrefixesAdvertisedIPv6 представляют максимальное число префиксов IPv4 и IPv6, которые узел хочет принять Майкрософт. Эти параметры можно изменять в любое время.