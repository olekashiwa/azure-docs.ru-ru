---
title: Восстановление хранилища данных после удаления
description: Руководство по восстановлению удаленного хранилища данных SQL Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e508eff3b322b49a6dc50d818c8bcccc3e924ff2
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759680"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Восстановление удаленного хранилища данных SQL Azure

В этой статье вы узнаете, как восстановить удаленное хранилище данных SQL с помощью портал Azure и PowerShell:

## <a name="before-you-begin"></a>Перед началом работы

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Проверьте ресурсы DTU.** Каждое хранилище данных SQL размещается на сервере SQL Server (например, myserver.database.windows.net), который имеет квоту DTU по умолчанию.  Убедитесь, что SQL Server имеет достаточное количество оставшихся квот DTU для восстанавливаемой базы данных. Чтобы узнать, как вычислить необходимое количество DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки для хранилища данных SQL](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Восстановление удаленного хранилища данных с помощью PowerShell

Чтобы восстановить удаленное хранилище данных SQL, используйте командлет [RESTORE-азсклдатабасе](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) . Если соответствующий логический сервер также был удален, вы не сможете восстановить это хранилище данных.

1. Перед началом убедитесь, что [установлен Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Откройте PowerShell.
3. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
4. Выберите подписку, содержащую удаленное хранилище данных для восстановления.
5. Получение конкретного удаленного хранилища данных.
6. Восстановление удаленного хранилища данных
    1. Чтобы восстановить удаленное хранилище данных SQL на другой логический сервер, убедитесь, что указано другое имя логического сервера.  Этот логический сервер также может находиться в другой группе ресурсов и регионе.
    1. Чтобы выполнить восстановление в другую подписку, используйте кнопку [переместить](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal) , чтобы переместить логический сервер в другую подписку.
1. Убедитесь, что восстановленное хранилище данных подключено.
1. После завершения восстановления можно настроить восстановленное хранилище данных, следуя [настройке базы данных после восстановления](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>" 
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Восстановление удаленной базы данных на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к серверу SQL Server, на котором размещено удаленное хранилище данных.
3. Выберите значок **Удаленные базы данных** в содержании.

    ![Удаленные базы данных](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Выберите удаленное хранилище данных SQL, которое необходимо восстановить.

    ![Выбор удаленных баз данных](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Укажите новое **имя базы данных** и нажмите кнопку **ОК** .

    ![Указание нового имени базы данных](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Следующие шаги
- [Восстановление существующего хранилища данных](sql-data-warehouse-restore-active-paused-dw.md)
- [Восстановление из хранилища геоархивных данных](sql-data-warehouse-restore-from-geo-backup.md)