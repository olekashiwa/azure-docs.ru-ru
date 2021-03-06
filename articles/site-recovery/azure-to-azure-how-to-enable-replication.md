---
title: Настройка репликации для виртуальных машин Azure в Azure Site Recovery
description: Узнайте, как настроить репликацию в другой регион для виртуальных машин Azure с помощью Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973691"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Репликация виртуальных машин Azure в другой регион Azure


В этой статье описывается, как включить репликацию виртуальных машин Azure между регионами Azure.

## <a name="before-you-start"></a>Перед началом работы

В этой статье предполагается, что вы подготовились к Site Recoveryному развертыванию, как описано в [руководстве по аварийному восстановлению из Azure в Azure](azure-to-azure-tutorial-enable-replication.md).

Должны быть выполнены необходимые условия, и необходимо создать хранилище служб восстановления.


## <a name="enable-replication"></a>Включение репликации

Включите репликацию. Предполагается, что основной регион Azure — это "Восточная Азия", а дополнительный — "Юго-Восточная Азия".

1. В хранилище щелкните **+Replicate** (+Репликация).
2. Обратите внимание на следующие поля:
   - **Источник** — источник происхождения виртуальных машин, в этом случае это **Azure**.
   - **Исходное расположение**: регион Azure, из которого вы хотите защитить виртуальные машины. В этом примере исходным расположением является "Восточная Азия".
   - **Модель развертывания** — модель развертывания Azure для исходных компьютеров.
   - **Исходная подписка**: подписка, к которой принадлежат исходные виртуальные машины. Это может быть любая подписка в одном клиенте Azure Active Directory, где существует хранилище служб восстановления.
   - **Группа ресурсов** — группа ресурсов, к которой принадлежат исходные виртуальные машины. Для защиты на следующем шаге будут перечислены все виртуальные машины в выбранной группе ресурсов.

     ![Включение репликации](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. В разделе **Виртуальные машины > Выбор виртуальных машин** выберите все виртуальные машины, которые нужно реплицировать. Можно выбрать только те компьютеры, для которых можно включить репликацию. Нажмите кнопку **ОК**.
    ![Включение репликации](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. В разделе **Параметры** можно при необходимости настроить параметры целевого веб-сайта:

   - **Целевое расположение**. Расположение, куда будут реплицированы данные исходных виртуальных машин. В зависимости от расположения выбранной машины Site Recovery предоставит список подходящих целевых регионов. Мы рекомендуем сохранить такое же целевое расположение, что и расположение хранилищ служб восстановления.
   - **Целевая подписка**. Целевая подписка, используемая для аварийного восстановления. По умолчанию целевая подписка будет совпадать с исходной подпиской.
   - **Целевая группа ресурсов**. Группа ресурсов, в которой будут находиться все реплицированные виртуальные машины.
       - По умолчанию Site Recovery создает новую группу ресурсов в целевом регионе с суффиксом ASR в имени.
       - Если группа ресурсов, созданная Site Recovery, уже существует, она будет использована повторно.
       - Можно настроить параметры группы ресурсов.
       - Расположением целевой группы ресурсов может быть любой регион Azure, кроме региона, в котором размещаются исходные виртуальные машины.
   - **Целевая виртуальная сеть**. по умолчанию Site Recovery создает новую виртуальную сеть в целевом регионе с суффиксом ASR в имени. Она будет сопоставлена с исходной сетью и будет использоваться для защиты в будущем. [Узнайте больше](site-recovery-network-mapping-azure-to-azure.md) о сетевом сопоставлении.
   - **Целевые учетные записи хранения (исходная виртуальная машина не использует управляемые диски)** . по умолчанию Site Recovery создает новую целевую учетную запись хранения, копируя конфигурацию хранилища исходной виртуальной машины. В случае, если учетная запись хранения уже существует, она используется повторно.
   - **Управляемые репликами диски (исходная виртуальная машина использует управляемые диски)** . Site Recovery создает в целевом регионе новые диски, управляемые репликами, для отражения управляемых дисков исходной виртуальной машины с тем же типом хранилища (Standard или Premium), что и управляемый диск исходной виртуальной машины.
   - **Cache Storage accounts** (Учетные записи хранения кэша). Для Site Recovery необходима дополнительная учетная запись хранения, называемая учетной записью хранения кэша, в исходном регионе. Все изменения, происходящие на исходных виртуальных машинах, отслеживаются и отправляются учетной записи хранения кэша перед репликацией в целевое расположение. Эта учетная запись хранения должна быть стандартной.
   - **Целевые группы доступности**. по умолчанию Site Recovery создает новую группу доступности в целевом регионе с суффиксом ASR в имени для виртуальных машин, входящих в группу доступности в исходном регионе. Если группа доступности, созданная с помощью Site Recovery, уже существует, она используется повторно.
   - **Целевые зоны доступности.** По умолчанию Site Recovery назначает один и тот же номер зоны как исходный регион в целевом регионе, если целевой регион поддерживает зоны доступности.

     Если целевой регион не поддерживает зоны доступности, целевые виртуальные машины настраиваются как отдельные экземпляры по умолчанию. При необходимости можно включить такие виртуальные машины в группы доступности в целевом регионе. Для этого нажмите кнопку "Настроить".

     >[!NOTE]
     >После включения репликации изменить тип доступности (один экземпляр), группу доступности или зону доступности нельзя. Чтобы изменить тип доступности, необходимо отключить и повторно включить репликацию.
     >

   - **Политика репликации** определяет параметры для журнала хранения точки восстановления и периодичность создания моментальных снимков, совместимых с приложениями. По умолчанию Azure Site Recovery создает новую политику репликации с параметрами по умолчанию "24 часа" для хранения точки восстановления и "4 часа" для периодичности создания моментальных снимков с постоянным приложением.

     ![Включение репликации](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Включить репликацию для добавленных дисков

При добавлении дисков на виртуальную машину Azure, для которой включена репликация, происходит следующее.
-   При репликации работоспособности виртуальной машины отображается предупреждение, а также уведомление о том, что один или несколько дисков доступны для защиты.
-   Если включить защиту для добавленных дисков, предупреждение исчезнет после начальной репликации диска.
-   Если вы решили не включать репликацию для диска, можно отклонить предупреждение.


    ![Добавлен новый диск](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Чтобы включить репликацию для добавленного диска, выполните следующие действия.

1.  В хранилище > **реплицированные элементы**щелкните виртуальную машину, в которую был добавлен диск.
2.  Щелкните **диски**, а затем выберите диск данных, для которого требуется включить репликацию (эти диски имеют состояние **не защищено** ).
3.  В окне **сведения о диске**щелкните **включить репликацию**.

    ![Включить репликацию для добавленного диска](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

После запуска задания включения репликации и завершения начальной репликации будет удалено предупреждение о работоспособности репликации для проблемы с диском.



## <a name="customize-target-resources"></a>Настройка целевых ресурсов

Можно изменить целевые параметры по умолчанию, используемые Site Recovery.

1. Нажмите кнопку **Настройка** рядом с пунктом "Целевая подписка", чтобы изменить целевую подписку по умолчанию. Выберите подписку из списка подписок, доступных в одном клиенте Azure Active Directory (AAD).

2. Щелкните **Настроить**, чтобы изменить параметры по умолчанию:
    - **Целевая группа ресурсов**. Выберите группу ресурсов из списка групп ресурсов в целевом расположении подписки.
    - **Target Virtual Network** (Целевая виртуальная сеть). Выберите сеть из списка всех виртуальных сетей в целевой локации.
    - **Группа доступности**. Здесь можно добавить параметры группы доступности для виртуальной машины, если она является частью группы доступности исходного региона.
    - **Target Storage accounts** (Целевые учетные записи хранения). Выберите учетную запись хранения, которую нужно использовать.

        ![Включение репликации](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Щелкните **Настроить**, чтобы изменить настройки репликации.
4. В области **согласованность с несколькими виртуальными машинами**выберите виртуальные машины, которые необходимо реплицировать вместе.
    - На всех компьютерах в группе репликации будут общие отказоустойчивые и согласованные точки восстановления после отработки отказа.
    - Включение согласованности с несколькими виртуальными машинами может повлиять на производительность рабочей нагрузки (так как это сильно загружает ЦП). Его следует включать только в том случае, если на компьютерах выполняется одна и та же Рабочая нагрузка и требуется согласованность на нескольких компьютерах.
    - Например, если приложение содержит 2 SQL Server виртуальные машины и два веб-сервера, следует добавить только SQL Server виртуальные машины в группу репликации.
    - В группе репликации можно выбрать не более 16 виртуальных машин.
    - Если включить согласованность между виртуальными машинами, компьютеры в группе репликации будут обмениваться данными друг с другом через порт 20004.
    - Убедитесь, что устройство брандмауэра не блокирует внутреннюю связь между виртуальными машинами через порт 20004.
    - Если требуется, чтобы виртуальные машины Linux были частью группы репликации, необходимо вручную открыть исходящий трафик через порт 20004 в соответствии с указаниями для конкретной версии Linux.
![Включение репликации](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Щелкните **Create target resource** (Создать целевой ресурс) > **Включить репликацию**.
6. После включения репликации виртуальных машин можно проверить их состояние работоспособности в разделе **Реплицированные элементы**.

>[!NOTE]
>Во время начальной репликации обновление состояния может занять некоторое время (без видимого прогресса). Щелкните **Обновить**, чтобы получить последние сведения о состоянии задания.
>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о выполнении тестовой отработки отказа см. в [этой статье](site-recovery-test-failover-to-azure.md).
