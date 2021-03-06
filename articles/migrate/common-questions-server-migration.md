---
title: Распространенные вопросы о миграции сервера с миграцией в Azure
description: Получите ответы на часто задаваемые вопросы о миграции сервера в Azure Migration
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067387"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Миграция сервера миграция Azure: распространенные вопросы

В этой статье содержатся ответы на часто задаваемые вопросы о переносе сервера в Azure. При наличии дальнейших запросов после прочтения этой статьи опубликуйте их на [форуме по миграции Azure](https://aka.ms/AzureMigrateForum). Если у вас есть другие вопросы, ознакомьтесь со следующими статьями:

- [Общие вопросы](resources-faq.md) о службе "миграция Azure".
- [Вопросы](common-questions-appliance.md) об устройстве миграции Azure.
- [Вопросы](common-questions-discovery-assessment.md) об обнаружении, оценке и визуализации зависимостей.


## <a name="how-does-agentless-vmware-replication-work"></a>Как работает репликация VMware без агента?

Метод репликации без агента для VMware использует моментальные снимки VMware, а VMware — отслеживание измененных блоков (CBT). Начальный цикл репликации планируется, когда пользователь запускает репликацию. В первом цикле репликации создается моментальный снимок виртуальной машины, и этот моментальный снимок используется для репликации VMDK-дисков виртуальных машин. По завершении цикла начальной репликации разностные циклы репликации планируются периодически. В цикле разностной репликации создается моментальный снимок, а также происходит репликация блоков данных, которые изменились с момента предыдущего цикла репликации. Отслеживание измененных блоков VMware используется для определения блоков, которые были изменены с момента последнего цикла.
Частота периодических циклов репликации автоматически управляется службой в зависимости от того, сколько других виртуальных машин и дисков одновременно реплицируется из одного хранилища данных, а в идеале — 1 цикл в час на каждую виртуальную машину.

Во время миграции планируется выполнить цикл репликации по запросу, чтобы виртуальная машина захватывает все оставшиеся данные. Можно выбрать завершение работы виртуальной машины в процессе миграции, чтобы обеспечить целостность данных и согласованность приложений.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Почему параметр повторной синхронизации не отображается в стеке без агента?

В стеке без агента во всех разностных циклах мы переносите различие между текущим снимком и предыдущим снимком, который мы использовали. Поскольку это всегда разница между моментальными снимками, это дает преимущество при свертывании данных (т. е. Если определенный сектор записывается "n" раз между моментальными снимками, нам нужно только переместить последнюю запись, так как мы заинтересованы только в последней синхронизации). Это отличается от стека на основе агента, в котором мы отслеживающим каждую запись и применяем их. Это означает, что каждый разностный цикл является повторной синхронизацией. Поэтому параметр повторной синхронизации не предоставляется. 

Если все диски не синхронизированы из-за сбоя, они фиксируются в следующем цикле. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Каково влияние скорости обновления, если я использую бесагентную репликацию?

Так как стек зависит от того, какие данные были сведены, то в этом стеке важна схема обновления. Шаблон, в котором файл записывается повторно и опять не оказывает никакого воздействия. Однако шаблон, в котором записан каждый другой сектор, приведет к высоким изменениям в следующем цикле. Так как мы максимально сокращаем объем передаваемых данных, мы разрешают, чтобы данные выходили как можно больше, прежде чем планировать следующий цикл.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Как часто планируется цикл репликации?

Формула для планирования следующего цикла репликации: (предыдущее время цикла/2) или 1 час в зависимости от того, что больше. Например, если для разностного цикла виртуальная машина заняла четыре часа, мы планируем его следующий цикл в течение 2 часов, а не в течение следующего часа. Это отличается в том случае, если цикл находится сразу после IR, где мы планируем первый разностный цикл немедленно.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Каково влияние на производительность vCenter Server или узла ESXi при использовании репликации без агента?

Так как в случае безагентной репликации используются моментальные снимки, для хранения данных будет потреблено операций ввода-вывода в секунду, и клиентам потребуется некоторое количество операций ввода-вывода в хранилище. Мы не рекомендуем использовать этот стек в ограниченном окружении хранилища и операций ввода-вывода.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>Поддерживает ли стек миграции без агента перенос виртуальных машин UEFI в виртуальные машины Azure Gen 2?

Нет, для переноса этих виртуальных машин на виртуальные машины Azure поколения 2 необходимо использовать Azure Site Recovery. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Можно ли закрепить мои виртуальные машины для Зоны доступности Azure при миграции?

Нет, поддержка Зоны доступности Azure отсутствует.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Какой транспортный протокол используется при миграции Azure во время репликации?

Служба "миграция Azure" использует протокол сетевого блока (NBD) с шифрованием SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Какова минимальная версия vCenter Server, необходимая для миграции?

Необходимо иметь по крайней мере vCenter Server 5,5 и VMware vSphere узла ESXi версии 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Могут ли клиенты переносить свои виртуальные машины на неуправляемые диски?

Нет. Служба "миграция Azure" поддерживает миграцию только на управляемые диски (стандартный HDD, SSD уровня "Премиум").

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Сколько виртуальных машин можно реплицировать одновременно с помощью стека VMware без агента?

Сейчас клиенты могут одновременно переносить 100 виртуальных машин на vCenter Server. Это можно сделать с пакетами по 10 виртуальными машинами.




