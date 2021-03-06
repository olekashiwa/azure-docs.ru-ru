---
title: Повторное включение защиты виртуальных машин VMware на локальном сайте с помощью Azure Site Recovery
description: Узнайте, как повторно включить защиту виртуальных машин VMware после отработки отказа в Azure с помощью Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498111"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Повторное включение защиты: Azure — локальная среда

После [отработки отказа](site-recovery-failover.md) локальных виртуальных машин VMware или физических серверов в Azure, чтобы восстановить размещение на локальном сайте, сначала нужно повторно защитить виртуальные машины Azure, созданные во время отработки отказа. Из этой статьи вы узнаете, как это сделать. 

## <a name="before-you-begin"></a>Перед началом работы

1. Выполните действия, описанные в [этой статье](vmware-azure-prepare-failback.md) , чтобы подготовиться к повторной защите и восстановлению размещения, включая настройку сервера обработки в Azure и локального главного целевого сервера, а также настройку VPN типа "сеть — сеть" или частный пиринг ExpressRoute для восстановления размещения.
2. Убедитесь, что локальный сервер конфигурации работает и подключен к Azure. Во время отработки отказа в Azure локальный сайт может быть недоступен, а сервер конфигурации может быть недоступен или выключен. Во время восстановления размещения виртуальная машина должна существовать в базе данных сервера конфигурации. В противном случае восстановление размещения завершается сбоем.
3. Удалите все моментальные снимки на локальном главном целевом сервере. Повторное защита не будет работать, если имеются моментальные снимки.  Моментальные снимки на виртуальной машине автоматически объединяются во время задания повторной защиты.
4. Если вы повторно защищаете виртуальные машины, собранные в группу репликации, для согласованности с несколькими виртуальными машинами, убедитесь, что все они имеют одинаковую операционную систему (Windows или Linux), и убедитесь, что на главном целевом сервере используется операционная система одинакового типа. Все виртуальные машины в группе репликации должны использовать один и тот же главный целевой сервер.
5. Откройте [необходимые порты](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) для восстановления размещения.
6. Перед восстановлением размещения убедитесь, что vCenter Server подключено. В противном случае отключение дисков и их присоединение к виртуальной машине завершится сбоем.
7. Если сервер vCenter управляет виртуальными машинами, для которых выполняется откат, убедитесь, что у вас есть необходимые разрешения. Если выполнить обнаружение vCenter в режиме пользователя только для чтения и защитить виртуальные машины, операция настройки защиты завершится успешно и отработка отказа будет работать. Однако во время повторного включения защиты происходит сбой отработки отказа, так как хранилища данных не могут быть обнаружены и не перечислены во время повторного включения защиты. Чтобы устранить эту проблему, можно обновить учетные данные vCenter с помощью [соответствующей учетной записи или разрешений](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery), а затем повторить задание. 
8. Если вы использовали шаблон для создания виртуальных машин, убедитесь, что каждая виртуальная машина имеет собственный UUID для дисков. Если локальный UUID виртуальной машины конфликтует с UUID главного целевого сервера, поскольку оба были созданы из одного и того же шаблона, повторное включение защиты завершится сбоем. Выполните развертывание из другого шаблона.
9. Если вы не выполняете возврат на другой vCenter Server, убедитесь, что на нем обнаружен новый vCenter Server и главный целевой сервер. Обычно, если они не являются хранилищами данных, недоступны или не отображаются при повторном включении **защиты**.
10. Проверьте следующие сценарии, в которых невозможно выполнить откат.
    - Если вы используете бесплатный выпуск ESXi 5,5 или vSphere 6 для низкоуровневой оболочки. Обновите версию до другой.
    - При наличии физического сервера Windows Server 2008 R2 с пакетом обновления 1 (SP1).
    - Виртуальные машины VMware не могут восстановить работоспособность в Hyper-V.
    - Виртуальные машины, которые [были перенесены](migrate-overview.md#what-do-we-mean-by-migration).
    - Виртуальная машина, которая была перемещена в другую группу ресурсов.
    - Реплика виртуальной машины Azure, которая была удалена.
    - Реплика виртуальной машины Azure, которая не защищена (реплицируется на локальный сайт).
10. [Проверьте типы восстановления размещения](concepts-types-of-failback.md) , которые можно использовать — восстановление исходного расположения и восстановление в альтернативное расположение.


## <a name="enable-reprotection"></a>Включение повторной защиты

Включите репликацию. Вы можете повторно защитить определенные виртуальные машины или план восстановления:

- При повторном включении защиты плана восстановления необходимо указать значения для каждой защищенной машины.
- Если виртуальные машины принадлежат группе репликации для согласованности нескольких виртуальных машин, их можно защитить только с помощью плана восстановления. Виртуальные машины в группе репликации должны использовать один и тот же главный целевой сервер.

### <a name="before-you-start"></a>Перед началом работы

- После загрузки виртуальной машины в Azure после отработки отказа агенту потребуется некоторое время на его повторную регистрацию на сервере конфигурации (до 15 минут). В течение этого времени нельзя включать повторную защиту. На экране отобразится сообщение о том, что агент не установлен. В этом случае подождите несколько минут и снова включите защиту.
- Если вы хотите восстановить размещение ВИРТУАЛЬНОЙ машины Azure на существующую локальную виртуальную машину, подключите локальные хранилища данных виртуальных машин с доступом на чтение и запись на узле ESXi главного целевого сервера.
- Если вы хотите выполнить восстановление размещения в альтернативное расположение, например если локальная виртуальная машина не существует, выберите диск хранения и хранилище данных, настроенные для главного целевого сервера. При восстановлении размещения на локальном сайте виртуальные машины VMware в плане защиты восстановления размещения используют то же хранилище данных, что и главный целевой сервер. Затем в vCenter создается новая виртуальная машина.

Включите повторное включение защиты следующим образом.

1. Щелкните **Хранилище** > **Реплицированные элементы**. Щелкните виртуальную машину, для которой проводилась отработка отказа, правой кнопкой мыши и выберите **Защитить повторно**. Вы также можете щелкнуть компьютер и выбрать параметр **Защитить повторно**, используя кнопки управления.
2. Убедитесь, что в качестве направления защиты выбрано **Из Azure на локальный компьютер**.
3. В полях **Главный целевой сервер** и **Сервер обработки** выберите локальный главный целевой сервер и сервер обработки.  
4. В поле **Хранилище данных** выберите хранилище данных, для которого нужно восстановить диски локальной среды. Используйте этот параметр, если локальная виртуальная машина была удалена и вам нужно создать диски. Этот параметр не учитывается, если диски уже существуют. Необходимо указать значение.
5. Выберите диск хранения.
6. Политика восстановления размещения выбирается автоматически.
7. Нажмите кнопку **ОК**, чтобы начать процесс повторной защиты.

    ![Диалоговое окно "Повторная защита"](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Задание начинает реплицировать виртуальную машину Azure на локальный сайт. Ход выполнения операции можно отслеживать на вкладке **Задания** .
    - После завершения повторного включения защиты виртуальная машина переходит в защищенное состояние.
    - Во время повторного включения защиты локальная виртуальная машина отключается. Это позволяет обеспечить согласованность данных во время репликации.
    - Не включайте локальную виртуальную машину после завершения повторной защиты.
   

## <a name="next-steps"></a>Дальнейшие действия

- Если возникнут проблемы, см. [статью Устранение неполадок](vmware-azure-troubleshoot-failback-reprotect.md).
- После защиты виртуальных машин Azure можно [выполнить восстановление размещения](vmware-azure-failback.md). Восстановление размещения завершает работу ВИРТУАЛЬНОЙ машины Azure и загружает локальную виртуальную машину. Предполагалось некоторое время простоя приложения и выберите соответствующее время восстановления размещения.


