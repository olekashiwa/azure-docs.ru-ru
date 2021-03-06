---
title: Поддержка добавления виртуальных машин Azure в существующую группу доступности | Документы Майкрософт
description: Эта статья содержит матрицу поддержки, в которой можно смешивать серии виртуальных машин в одной группе доступности.
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122926"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Поддержка добавления виртуальных машин Azure в существующую группу доступности

Иногда вы можете столкнуться с ограничениями при добавлении новых виртуальных машин в существующую группу доступности. На следующей диаграмме показано, какие серии виртуальных машин можно объединить в одной группе доступности.

Ниже приводится матрица поддержки для объединения различных типов виртуальных машин:

Серии и группа доступности|Вторая виртуальная машина|Объект|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Первая виртуальная машина|||||||
|Объект||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Все остальные серии не могут находиться в одной и той же группе доступности, так как для них требуется определенное оборудование.

Размер виртуальной машины A8/A9 не может быть смешан из-за необходимости в выделенной внутренней сети RDMA.
