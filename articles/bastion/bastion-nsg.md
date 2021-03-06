---
title: Работа с виртуальными машинами и группы безопасности сети в Azure бастиона
description: В этой статье описывается, как внедрить NSG Access с помощью Azure бастиона
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087273"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Работа с NSG Access и Azure бастиона

При работе с Azure бастиона можно использовать группы безопасности сети (группы безопасности сети). Дополнительные сведения см. в разделе [группы безопасности](../virtual-network/security-overview.md). 

![Architecture](./media/bastion-nsg/nsg-architecture.png)

На этой схеме:

* Узел бастиона развертывается в виртуальной сети.
* Пользователь подключается к порталу Azure с помощью любого браузера HTML5.
* Пользователь переходит на виртуальную машину Azure по протоколу RDP или SSH.
* Интеграция подключения — один щелчок RDP или SSH в браузере
* На виртуальной машине Azure не требуется общедоступный IP-адрес.

## <a name="nsg"></a>Группы безопасности сети

В этом разделе показан сетевой трафик между пользователем и Azure бастиона, а также для целевых виртуальных машин в виртуальной сети.

### <a name="azurebastionsubnet"></a>азуребастионсубнет

Azure бастиона развертывается специально для Азуребастионсубнет.

* **Входящий трафик:**

   * **Входящий трафик из общедоступного Интернета:** Бастиона Azure создаст общедоступный IP-адрес, для которого в общедоступном IP-адресе входящего трафика должен быть включен порт 443. Порт 3389/22 не требуется открывать на Азуребастионсубнет.
   * **Входящий трафик из плоскости управления бастиона Azure:** Для подключения плоскости управления включите порт 443 для входящего трафика из тега службы **гатевайманажер** . Это позволяет плоскости управления, то есть, диспетчером шлюзов взаимодействовать с Azure бастиона.

* **Исходящий трафик:**

   * **Исходящий трафик к целевым виртуальным машинам:** Azure бастиона будет обращаться к целевым виртуальным машинам через частный IP-адрес. Группы безопасности сети должен разрешить исходящий трафик в другие целевые подсети виртуальных машин для портов 3389 и 22.
   * **Исходящий трафик к другим общедоступным конечным точкам в Azure:** Azure бастиона должен иметь возможность подключения к различным общедоступным конечным точкам в Azure (например, для хранения журналов диагностики и журналов отслеживания использования). По этой причине Azure бастиона требуется исходящий трафик от 443 к тегу службы **AzureCloud** .

* **Целевая подсеть ВИРТУАЛЬНОЙ машины:** Это подсеть, содержащая целевую виртуальную машину, к которой необходимо подключиться по протоколу RDP или SSH.

   * **Входящий трафик из Azure бастиона:** Azure бастиона будет обращаться к целевой виртуальной машине через частный IP-адрес. Порты RDP/SSH (порты 3389/22 соответственно) должны быть открыты на стороне целевой виртуальной машины через частный IP-адрес. В этом правиле рекомендуется добавить диапазон IP-адресов подсети бастиона для Azure, чтобы разрешить только бастиона открывать эти порты на целевых виртуальных машинах в вашей целевой подсети виртуальной машины.

## <a name="apply"></a>Применение группы безопасности сети к Азуребастионсубнет

Если вы создали и примените NSG к ***азуребастионсубнет***, убедитесь, что в NSG добавлены следующие правила. Если не добавить эти правила, создание или обновление NSG завершится ошибкой:

* **Подключение к плоскости управления:** Входящий трафик на 443 из Гатевайманажер
* **Ведение журнала диагностики и другие:** Исходящий трафик с 443 по AzureCloud. Региональные теги в этом теге службы пока не поддерживаются.
* **Целевая виртуальная машина:** Исходящий трафик для 3389 и 22 в VirtualNetwork

Пример правила NSG доступен для справки в этом [шаблоне](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)быстрого запуска.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure бастиона см. в разделе [часто задаваемые вопросы](bastion-faq.md).
