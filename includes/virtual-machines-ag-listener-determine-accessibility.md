---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185240"
---
Важно понимать, что есть два способа настройки прослушивателя группы доступности в Azure. Они отличаются типом подсистемы балансировки нагрузки Azure, используемой при создании прослушивателя. Различия описаны в следующей таблице:

| Тип подсистемы балансировки нагрузки | Реализация | Используйте в следующих ситуациях: |
| --- | --- | --- |
| **Внешний** |Использование *общедоступного виртуального IP-адреса* облачной службы, на которой размещены виртуальные машины. |Необходимо, чтобы прослушиватель был доступен за пределами виртуальной сети, в том числе через Интернет. |
| **Внутренний** |Использование *внутренней подсистемы балансировки нагрузки* с частным адресом для прослушивателя. |Необходимо, чтобы прослушиватель был доступен только в этой виртуальной сети. Сюда относится VPN типа "сеть — сеть" в гибридных сценариях. |

> [!IMPORTANT]
> Если прослушиватель использует общедоступный VIP-адрес облачной службы (внешний балансировщик нагрузки), то при условии, что клиент, прослушиватель и база данных находятся в одном регионе Azure, вам не будет начисляться плата за исходящий трафик. В противном случае все данные, возвращаемые через прослушиватель, считаются исходящим трафиком и оплачиваются по обычным тарифам на передачу данных. 
> 
> 

Внутреннюю подсистему балансировки нагрузки можно настроить только в виртуальных сетях регионального масштаба. В имеющихся виртуальных сетях, настроенных для территориальной группы, невозможно использовать внутреннюю балансировку нагрузки. Дополнительные сведения см. в статье [Обзор внутренней подсистемы балансировки нагрузки](../articles/load-balancer/load-balancer-internal-overview.md).

