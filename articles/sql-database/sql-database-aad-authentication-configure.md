---
title: Настройка аутентификации Azure Active Directory
description: Узнайте, как подключиться к базе данных SQL, управляемому экземпляру и хранилищу данных SQL с помощью Azure Active Directory проверки подлинности после настройки Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: dc2661bbc443201d6a2da4b5efb7ecdc2caad444
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732575"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Настройка и администрирование аутентификации Azure Active Directory с помощью SQL

В этой статье показано, как создать и заполнить Azure AD, а затем использовать Azure AD с [базой данных SQL](sql-database-technical-overview.md)Azure, [управляемым экземпляром](sql-database-managed-instance.md)и [хранилищем данных SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Обзор см. в статье [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Эта статья относится к Azure SQL Server, а также к базам данных SQL и хранилища данных SQL, создаваемым на сервере SQL Azure. Для простоты база данных SQL используется как для базы данных SQL, так и для хранилища данных SQL.

> [!IMPORTANT]  
> Подключение к SQL Server на виртуальной машине Azure с использованием учетной записи Azure Active Directory не поддерживается. Вместо этого используйте учетную запись домена Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Создание и заполнение каталога Azure AD

Создайте каталог Azure AD и заполните его пользователями и группами. Azure AD может быть исходным управляемым доменом Azure AD. Azure AD может также быть локальной доменной службой Active Directory, объединенной в федерацию с Azure AD.

Дополнительные сведения см. в статьях [Интеграция локальных удостоверений с Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Добавление имени личного домена в Azure Active Directory](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory), [Управление каталогом Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Azure Active Directory Cmdlets](/powershell/azure/overview) (Командлеты для Azure Active Directory) и [Порты и протоколы, необходимые для гибридной идентификации](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Связывание подписки Azure с Azure Active Directory или добавление ее в службу

1. Свяжите свою подписку Azure с Azure Active Directory, сделав каталог доверенным для подписки Azure, в которой размещена база данных. Дополнительные сведения см. в статье о [связи между подписками Azure и Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Используйте переключатель каталогов на портале Azure, чтобы перейти к подписке, связанной с предметной областью.

   > [!IMPORTANT]
   > Между каждой подпиской Azure и экземпляром Azure AD установлено отношение доверия. Это означает, что она доверяет каталогу проверять подлинность пользователей, служб и устройств. Несколько подписок могут доверять одному и тому же каталогу, но одна конкретная подписка доверяет только одному каталогу. Данное отношение доверия, которое подписка имеет с каталогом, отличается от отношения, которую подписка имеет со всеми другими ресурсами в Azure (веб-сайтами, базами данных и т. д.), которые больше похожи на дочерние ресурсы подписки. Если срок действия подписки истекает, доступ к другим ресурсам, связанным с этой подпиской, также прекращается. Однако каталог останется в Azure, вы можете связать другую подписку с этим каталогом и продолжать управлять пользователями каталога. Дополнительные сведения о ресурсах см. в статье, посвященной [доступу к ресурсам в Azure](../active-directory/active-directory-b2b-admin-add-users.md). Дополнительные сведения об этих отношениях доверия см. в статье о [связывании подписки Azure с Azure Active Directory или добавлении ее в службу](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Создание администратора Azure AD для сервера Azure SQL Server

Каждый сервер Azure SQL Server (на котором размещена база данных SQL или хранилище данных SQL) сначала имеет одну учетную запись администратора сервера, который является администратором всего сервера Azure SQL Server. Необходимо создать второго администратора SQL Server, то есть учетную запись Azure AD. Этот участник создается как пользователь автономной базы данных в базе данных master. Администраторы с учетными записями администратора сервера являются членами роли **db_owner** в каждой пользовательской базе данных. Они входят в каждую такую базу данных как пользователи **dbo**. Дополнительные сведения о ролях администратора базы данных см. в статье [Предоставление доступа к базе данных и управление им](sql-database-manage-logins.md).

При использовании Azure Active Directory с георепликацией необходимо настроить администратора Azure Active Directory для сервера-источника и сервера-получателя. Если администратор Azure Active Directory для сервера отсутствует, то пользователи, входящие в Active Directory, будут получать сообщение о невозможности подключиться к серверу.

> [!NOTE]
> Пользователи без учетной записи Azure AD (включая учетную запись администратора Azure SQL Server) не могут создавать пользователей на основе Azure AD, так как у них нет разрешения на проверку предложенных пользователей базы данных с помощью Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Предоставление администратора Azure Active Directory для управляемого экземпляра

> [!IMPORTANT]
> Выполните следующие действия, только если вы подготавливаете управляемый экземпляр. Эта операция может выполняться только глобально/администратором или администратором привилегированных ролей в Azure AD. Ниже описывается процесс предоставления разрешений пользователям, имеющим разные привилегии в каталоге.

> [!NOTE]
> Для администраторов Azure AD для MI, созданных до общедоступной версии, но продолжая работать с общедоступной учетной записью, не существует функционального изменения существующего поведения. Дополнительные сведения см. в разделе [Новая функциональность администратора Azure AD для MI](#new-azure-ad-admin-functionality-for-mi) .

Управляемому экземпляру требуются разрешения на чтение Azure AD для успешного выполнения таких задач, как проверка подлинности пользователей с помощью членства в группе безопасности или создание новых пользователей. Чтобы это работало, необходимо предоставить управляемому экземпляру разрешения на чтение Azure AD. Существуют два способа сделать это: с помощью портала и PowerShell. Ниже описываются оба способа.

1. В правом верхнем углу портала Azure щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory.

2. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию.

   С помощью этого шага можно связать подписку Active Directory с управляемым экземпляром, чтобы одна и та же подписка использовалась для Azure AD и управляемого экземпляра.

3. Перейдите к службе "Управляемый экземпляр Базы данных SQL" и выберите управляемый экземпляр, который вы хотите использовать для интеграции с Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Выберите баннер в верхней части страницы администрирования Active Directory и предоставьте разрешение текущему пользователю. Если вы вошли в Azure AD в качестве глобального или администратора компании, это можно сделать из портал Azure или с помощью PowerShell, выполнив приведенный ниже сценарий.

    ![Предоставление разрешений на портале](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. После успешного завершения операции в правом верхнем углу появится следующее уведомление.

    ![Успешное завершение](./media/sql-database-aad-authentication/success.png)

6. Теперь вы можете выбрать администратора Azure AD для управляемого экземпляра. Для этого на странице "Администратор Active Directory" щелкните **Задать администратора**.

    ![Задание администратора](./media/sql-database-aad-authentication/set-admin.png)

7. На странице добавления администратора AAD найдите пользователя, выберите пользователя (или группу), чтобы назначить его администратором, и щелкните **Выбрать**.

   На странице "Администратор Active Directory" отобразятся все участники и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. Список поддерживаемых администраторов приведен в разделе [Функции и ограничения Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Управление доступом на основе ролей (RBAC) применяется только к порталу Azure и не распространяется на SQL Server.

    ![Добавление администратора Azure Active Directory](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. В верхней части страницы "Администратор Active Directory" нажмите кнопку **Сохранить**.

    ![Сохранить](./media/sql-database-aad-authentication/save.png)

    Изменение администратора может занять несколько минут. После этого новый администратор появится в поле "Администратор Active Directory".

После подготовки администратора Azure AD для управляемого экземпляра можно приступить к созданию участников сервера Azure AD (имен входа) с помощью синтаксиса <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> . Дополнительные сведения см. в разделе [Общие сведения об управляемом экземпляре](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Чтобы потом удалить учетную запись администратора, в верхней части страницы "Администратор Active Directory" щелкните **Удалить администратора**, а затем нажмите кнопку **Сохранить**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Новые функции администратора Azure AD для MI

В следующей таблице приведена сводка функций для администратора входа Azure AD с открытым предварительным просмотром для MI, а также новые функции, предоставляемые с общедоступной версией для имен входа Azure AD.

| Администратор входа Azure AD для MI во время общедоступной предварительной версии | Функция общедоступного администратора Azure AD для MI |
| --- | ---|
| Ведет себя аналогично тому, как администратор Azure AD для базы данных SQL, который обеспечивает проверку подлинности Azure AD, но администратор Azure AD не может создавать имена входа Azure AD или SQL в базе данных master для MI. | Администратор Azure AD имеет разрешение sysadmin и может создавать имена входа AAD и SQL в базе данных master для MI. |
| Отсутствует в представлении sys. server_principals | Имеется в представлении sys. server_principals |
| Позволяет настроить отдельные гостевые пользователи Azure AD в качестве администратора Azure AD для MI. Дополнительные сведения см. [в разделе добавление Azure Active Directory пользователей службы совместной работы B2B в портал Azure](../active-directory/b2b/add-users-administrator.md). | Требуется создание группы Azure AD с гостевыми пользователями в качестве участников для настройки этой группы в качестве администратора Azure AD для MI. Дополнительные сведения см. [в статье поддержка Azure AD для](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support)бизнеса. |

Рекомендуется выполнить сброс администратора Azure AD с помощью портал Azure "удалить администратора" и "настроить администратора" для того же пользователя или группы Azure AD, как и для существующих администраторов Azure AD для MI, созданных до общедоступной версии.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Известные проблемы с именем входа Azure AD (GA) для MI

- Если имя входа Azure AD существует в базе данных master для MI, созданной с помощью команды T-SQL `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`, ее нельзя настроить в качестве администратора Azure AD для MI. При установке имени входа в качестве администратора Azure AD можно использовать команды портал Azure, PowerShell или интерфейса командной строки для создания имени входа Azure AD.
  - Имя входа должно быть удалено в базе данных master с помощью команды `DROP LOGIN [myaadaccount]`, прежде чем учетную запись можно будет создать как администратор Azure AD.
  - Настройте учетную запись администратора Azure AD в портал Azure после выполнения `DROP LOGIN`. 
  - Если не удается настроить учетную запись администратора Azure AD, проверьте имя входа в базе данных master управляемого экземпляра. Используйте следующую команду: `SELECT * FROM sys.server_principals`.
  - При настройке администратора Azure AD для MI автоматически будет создано имя входа в базе данных master для этой учетной записи. Удаление администратора Azure AD приведет к автоматическому удалению имени входа из базы данных master.

- Отдельные гостевые пользователи Azure AD не поддерживаются в качестве администраторов Azure AD для MI. Гостевые пользователи должны входить в группу Azure AD, чтобы быть настроенными в качестве администратора Azure AD. В настоящее время в колонке портал Azure не отображается серым гостевых пользователей для другого Azure AD, что позволяет пользователям продолжить настройку администратора. Сохранение гостевых пользователей в качестве администратора Azure AD приведет к сбою установки.
  - Если вы хотите сделать гостевой пользователь администратором Azure AD для MI, включите гостевой пользователь в группу Azure AD и задайте эту группу в качестве администратора Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell для управляемого экземпляра SQL

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager (RM) по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Модуль AzureRM продолжит принимать исправления ошибок до 2020 декабря.  Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Дополнительные сведения о совместимости см. [в разделе Введение в новый модуль Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Чтобы подготовить администратора Azure AD, выполните следующие команды Azure PowerShell.

- Connect-AzAccount
- SELECT-Азсубскриптион

Командлеты, используемые для инициализации и управления администратором Azure AD для управляемого экземпляра SQL:

| Имя командлета | Description |
| --- | --- |
| [Set-Азсклинстанцеактиведиректорядминистратор](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Подготовка администратора Azure AD для управляемого экземпляра SQL в текущей подписке. (Должно быть из текущей подписки)|
| [Remove-Азсклинстанцеактиведиректорядминистратор](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Удаляет администратора Azure AD для управляемого экземпляра SQL в текущей подписке. |
| [Get-Азсклинстанцеактиведиректорядминистратор](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Возвращает сведения о администраторе Azure AD для управляемого экземпляра SQL в текущей подписке.|

Следующая команда получает сведения об администраторе Azure AD для управляемого экземпляра с именем ManagedInstance01, связанного с группой ресурсов с именем ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Следующая команда подготавливает группу администраторов Azure AD с именем DBA для управляемого экземпляра с именем ManagedInstance01. Этот сервер связан с группой ресурсов ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Следующая команда удаляет администратора Azure AD для управляемого экземпляра с именем ManagedInstanceName01, связанного с группой ресурсов ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Вы также можете подготавливать экземпляр администратора Azure AD для управляемого экземпляра SQL, вызвав следующие команды интерфейса командной строки:

| Get-Help | Description |
| --- | --- |
|[AZ SQL MI AD — создание администратора](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Подготавливает администратора Azure Active Directory для управляемого экземпляра SQL. (Должно быть из текущей подписки) |
|[AZ SQL MI AD — администратор удаление](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Удаляет Azure Active Directory администратора для управляемого экземпляра SQL. |
|[AZ SQL MI AD — список администраторов](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Возвращает сведения о администраторе Azure Active Directory, настроенном в настоящее время для управляемого экземпляра SQL. |
|[AZ SQL MI AD — обновление администратора](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Обновляет Active Directory администратора для управляемого экземпляра SQL. |

Дополнительные сведения о командах CLI см. в разделе [AZ SQL MI](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Подготовка администратора Azure Active Directory для сервера Базы данных SQL Azure

> [!IMPORTANT]
> Выполните следующие действия, только если вы подготавливаете сервер Базы данных SQL Azure или хранилище данных Azure.

Ниже описаны две процедуры по подготовке администратора Azure Active Directory для сервера Azure SQL Server на портале Azure или с помощью PowerShell.

### <a name="azure-portal"></a>Портал Azure

1. В правом верхнем углу [портала Azure](https://portal.azure.com/) щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию. На этом шаге связанный с подпиской каталог Active Directory связывается с Azure SQL Server, чтобы одна и та же подписка использовалась для Azure AD и SQL Server. (На сервере Azure SQL Server может размещаться база данных SQL Azure или хранилище данных SQL Azure.)

    ![choose-ad][8]

2. Найдите и выберите **SQL Server**.

    ![Найдите и выберите серверы SQL Server.](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > На этой странице, прежде чем выбрать **Серверы SQL Server**, можно щелкнуть **звезду** рядом с именем, чтобы добавить категорию в *Избранное* и добавить **серверы SQL Server** на левую панель навигации.

3. На странице **SQL Server** выберите **Администратор Active Directory**.

4. На странице **Администратор Active Directory** щелкните **Задать администратора**.

    ![Серверы SQL Server, настроенные Active Directory Admin](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. На странице **Добавление администратора** найдите пользователя, выберите пользователя (или группу), чтобы назначить его администратором, и щелкните **Выбрать**. На странице "Администратор Active Directory" отобразятся все участники и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. (См. список поддерживаемых администраторов в разделе **функции и ограничения Azure AD** в статье [Использование Azure Active Directory проверки подлинности для проверки подлинности с помощью базы данных SQL или хранилища данных SQL](sql-database-aad-authentication.md).) Управление доступом на основе ролей (RBAC) применяется только к порталу и не распространяется на SQL Server.

    ![Выбор Azure Active Directory администратора](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. В верхней части страницы **Администратор Active Directory** нажмите кнопку **Сохранить**.

    ![сохранить администратора](./media/sql-database-aad-authentication/save-admin.png)

Изменение администратора может занять несколько минут. После этого новый администратор появится в поле **Администратор Active Directory** .

   > [!NOTE]
   > При настройке администратора Azure AD новое имя администратора (пользователя или группы) не может уже присутствовать в виртуальной базе данных master как пользователь аутентификации SQL Server. Если оно присутствует, настройка администратора Azure AD завершится ошибкой. Будет выполнен откат его создания и появится сообщение, что такой администратор (имя) уже существует. Такой пользователь аутентификации SQL Server не входит в Azure AD, поэтому любая попытка подключиться к серверу с помощью аутентификации Azure AD завершится сбоем.

Чтобы потом удалить учетную запись администратора, в верхней части страницы **Администратор Active Directory** щелкните **Удалить администратора**, а затем нажмите кнопку **Сохранить**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell для базы данных SQL Azure и хранилища данных SQL Azure

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Чтобы подготовить администратора Azure AD, выполните следующие команды Azure PowerShell.

- Connect-AzAccount
- SELECT-Азсубскриптион

Командлеты, используемые для инициализации и управления администратором Azure AD для базы данных SQL Azure и хранилища данных SQL Azure:

| Имя командлета | Description |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Выполняет подготовку учетной записи администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. (Должно быть из текущей подписки) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Удаляет учетную запись администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. |
| [Get-Азсклсерверактиведиректорядминистратор](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server или хранилища данных SQL Azure. |

Чтобы просмотреть дополнительные сведения о каждой из этих команд, используйте команду PowerShell Get-Help. Например, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Приведенный ниже скрипт выполняет подготовку группы администраторов Azure AD с именем **DBA_Group** (идентификатор объекта `40b79501-b343-44ed-9ce7-da4c8cc7353f`) для сервера **demo_server** в группе ресурсов с именем **Group-23**.

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Входной параметр **DisplayName** принимает отображаемое имя Azure AD или имя участника-пользователя. Например, ``DisplayName="John Smith"`` и ``DisplayName="johns@contoso.com"``. Для групп Azure AD поддерживается только отображаемое имя Azure AD.

> [!NOTE]
> Команда Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` не запрещает подготовку администраторов Azure AD для неподдерживаемых пользователей. Неподдерживаемого пользователя можно подготовить, но он не сможет подключиться к базе данных (См.

В следующем примере используется необязательный параметр **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Параметр **ObjectID** Azure AD является обязательным, если параметр **DisplayName** не уникален. Чтобы получить значения **ObjectID** и **DisplayName**, используйте раздел Active Directory классического портала Azure. Там можно просмотреть свойства пользователя или группы.

Следующий пример возвращает сведения о текущем администраторе Azure AD для сервера Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

В примере ниже выполняется удаление администратора Azure AD.

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Вы можете подготавливать администратора Azure AD, вызвав следующие команды интерфейса командной строки:

| Get-Help | Description |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Выполняет подготовку учетной записи администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. (Должно быть из текущей подписки) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Удаляет учетную запись администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server или хранилища данных SQL Azure. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Обновляет учетную запись администратора Active Directory для сервера SQL Server Azure или хранилища данных SQL Azure. |

Дополнительные сведения о командах интерфейса командной строки см. в разделе [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Кроме того, можно подготовить администратора Azure Active Directory с помощью интерфейсов REST API. Дополнительные сведения см. в справочнике по REST API управления службами и статье [Operations for Azure SQL Databases](/rest/api/sql/) (Операции для Базы данных SQL Azure).

## <a name="configure-your-client-computers"></a>Настройка клиентских компьютеров

Приложения или пользователи подключаются к Базе данных SQL Azure или хранилищу данных SQL, используя удостоверения Azure AD, с клиентских компьютеров. На эти компьютеры необходимо установить указанное ниже программное обеспечение.

- .NET Framework 4.6 или более поздней версии с [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Библиотека проверки подлинности Azure Active Directory для SQL Server (*ADAL. DLL*). Ниже приведены ссылки для загрузки для установки последнего драйвера SSMS, ODBC и OLE DB, содержащего *ADAL. Библиотека DLL* .
    1. [Среда SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [Драйвер ODBC 17 для SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB драйвер 18 для SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Вы можете выполнить эти требования, сделав следующее:

- Установка последней версии [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) или [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) соответствует требованиям .NET Framework 4,6.
    - SSMS устанавливает версию x86 для версии *ADAL. Библиотека DLL*.
    - SSDT устанавливает версию AMD64 библиотеки *ADAL. Библиотека DLL*.
    - Последняя версия Visual Studio из [файлов для загрузки Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) соответствует требованиям .NET Framework 4,6, но не устанавливает требуемую версию файлов *ADAL. Библиотека DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD

> [!IMPORTANT]
> Управляемый экземпляр теперь поддерживает участников сервера Azure AD (имен входа), которые позволяют создавать имена входа из пользователей, групп или приложений Azure AD. Участники сервера Azure AD (имена входа) предоставляют возможность проверки подлинности в управляемом экземпляре, не требуя создания пользователей базы данных в качестве пользователя автономной базы данных. Дополнительные сведения см. в разделе [Общие сведения об управляемом экземпляре](sql-database-managed-instance.md#azure-active-directory-integration). Сведения о синтаксисе для создания субъектов сервера (имен для входа) Azure AD см. в статье <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN (Transact-SQL)</a>.

Для проверки подлинности Azure Active Directory необходимо, чтобы пользователи базы данных создавались как пользователи автономной базы данных. Пользователь автономной базы данных на основе удостоверения Azure AD —это пользователь, у которого нет имени для входа в базу данных master и который сопоставляется с удостоверением в каталоге Azure AD, связанном с базой данных. Удостоверение Azure AD может быть учетной записью отдельного пользователя или группы. Дополнительные сведения о пользователях автономной базы данных см. в статье [Пользователи автономной базы данных — создание переносимой базы данных](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Пользователей баз данных (за исключением администраторов) невозможно создавать с помощью портала Azure. Роли RBAC не распространяются в SQL Server, Базу данных SQL или хранилище данных SQL. Роли Azure RBAC используются для управления ресурсами Azure и не относятся к разрешениям базы данных. Например, роль **Участник SQL Server** не предоставляет разрешение на подключение к Базе данных SQL или хранилищу данных SQL. Разрешение доступа должно быть предоставлено непосредственно в базе данных с помощью инструкций Transact-SQL.

> [!WARNING]
> Специальные символы, такие как двоеточие `:` или амперсанд `&`, когда они включены в качестве имен пользователей в операторах T-SQL CREATE LOGIN и CREATE USER, не поддерживаются.

Вы можете создать пользователя автономной базы данных на основе Azure AD (кроме администратора сервера, который является владельцем базы данных). Для этого подключитесь к базе данных с помощью удостоверения Azure AD от имени пользователя с уровнем разрешений не ниже, чем **Изменение любого пользователя**. Затем используйте следующий синтаксис Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

Значением параметра *Azure_AD_principal_name* может быть имя участника-пользователя Azure AD или отображаемое имя группы Azure AD.

**Примеры.** Создание пользователя автономной базы данных, представляющего собой пользователя федеративного или управляемого домена Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Чтобы создать пользователя автономной базы данных, представляющего собой группу Azure AD или группу федеративного домена, укажите отображаемое имя группы безопасности.

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Чтобы создать пользователя автономной базы данных, который представляет приложение и будет подключаться с помощью маркера Azure AD, выполните следующее.

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Для этой команды требуется, чтобы SQL Access Azure AD ("внешний поставщик") от имени вошедшего в систему пользователя. В некоторых случаях может возникнуть ситуация, из – за которой Azure AD возвращает исключение обратно в SQL. В таких случаях пользователь увидит ошибку SQL 33134, которая должна содержать сообщение об ошибке, специфичное для AAD. В большинстве случаев ошибка говорит о том, что доступ запрещен или пользователь должен зарегистрировать в MFA для доступа к ресурсу, или что доступ между сторонними приложениями должен обрабатываться через предварительную авторизацию. В первых двух случаях эта неполадка обычно вызвана политиками условного доступа, заданными в клиенте AAD пользователя. они не позволяют пользователю получить доступ к внешнему поставщику. Обновление политик ЦС для разрешения доступа к приложению "00000002-0000-0000-C000-000000000000" (идентификатор приложения AAD API Graph) должно устранить проблему. Если ошибка говорит о том, что доступ между приложениями первого производителя должен обрабатываться через предварительную авторизацию, это связано с тем, что пользователь вошел в качестве субъекта-службы. Команда должна быть выполнена, если вместо этого она выполняется пользователем.

> [!TIP]
> Создать пользователя напрямую из каталога Azure Active Directory можно только в том случае, если этот каталог связан с вашей подпиской Azure. Однако членов других каталогов Active Directory, являющихся импортированными пользователями в связанном каталоге Active Directory (так называемыми внешними пользователями), можно добавить в группу Active Directory в клиенте Active Directory. Создав пользователя автономной базы данных для этой группы AD, пользователи из внешнего каталога Active Directory могут получить доступ к базе данных SQL.

Подробные сведения о создании пользователей автономной базы данных на основе удостоверений Azure Active Directory см. в статье [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Если удалить учетную запись администратора Azure Active Directory на сервере Azure SQL Server, все прошедшие аутенитификацию пользователи Azure AD не смогут подключиться к серверу. При необходимости администратор Базы данных SQL может вручную удалить неиспользуемых пользователей Azure AD.

> [!NOTE]
> При получении ответа **Истек тайм-аут подключения**, возможно, потребуется задать для параметра `TransparentNetworkIPResolution` в строке подключения значение false. Дополнительные сведения см. в статье [Connection timeout issue with .NET Framework 4.6.1 — TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Проблема с временем ожидания подключения в .NET Framework 4.6.1 — TransparentNetworkIPResolution).

При создании пользователь базы данных получает разрешение **Подключение** и может подключаться к этой базе данных как участник роли **Public**. Изначально пользователю доступны только разрешения, предоставленные роли **Public**, или другие разрешения, предоставленные любой группе Azure AD, в которую входит пользователь. Подготовив пользователя автономной базы данных, использующей Azure AD, можно предоставить ему дополнительные разрешения — так же, как разрешения для любого другого типа пользователя. Обычно разрешения предоставляются ролям базы данных, а затем эти роли назначаются пользователям. Подробные сведения см. в статье [Database Engine Permission Basics](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) (Основные сведения о разрешениях ядра СУБД). Дополнительные сведения о специальных ролях базы данных SQL см. в статье [Проверка подлинности и авторизация в базе данных SQL Azure: предоставление доступа](sql-database-manage-logins.md).
Для учетной записи пользователя федеративного домена, импортируемая в управляемый домен, следует использовать идентификатор управляемого домена.

> [!NOTE]
> Пользователи Azure AD помечаются в метаданных базы данных как тип E (EXTERNAL_USER), а группы — как тип X (EXTERNAL_GROUPS). Дополнительные сведения см. в статье [sys.database_principals (Transact-SQL)](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Подключение к пользовательской базе данных или хранилищу данных с помощью SSMS или SSDT  

Чтобы убедиться, что администратор Azure AD настроен правильно, подключитесь к базе данных **master** с помощью учетной записи администратора Azure AD.
Вы можете подготовить пользователя автономной базы данных, использующей Azure AD (кроме администратора сервера, который является владельцем базы данных). Для этого подключитесь к базе данных с помощью удостоверения Azure AD, у которого есть доступ к базе данных.

> [!IMPORTANT]
> Проверку подлинности Azure Active Directory поддерживают [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) для Visual Studio 2015. Выпуск SSMS за август 2016 года также включает в себя поддержку универсальной аутентификации Active Directory, что позволяет администраторам требовать прохождения многофакторной проверки подлинности с помощью телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Использование удостоверения Azure AD для подключения с помощью SSMS или SSDT

Ниже описаны действия по подключению к базе данных SQL с помощью удостоверения Azure AD с использованием SQL Server Management Studio или инструментов баз данных SQL Server.

### <a name="active-directory-integrated-authentication"></a>Встроенная аутентификация Active Directory

Этот метод следует использовать, если вы входите в систему Windows с помощью учетных данных Azure Active Directory из федеративного домена.

1. Запустите Management Studio или Data Tools и в диалоговом окне **Подключение к серверу** (или **Подключиться к ядру СУБД**) в поле **Проверка подлинности** выберите **Active Directory — встроенная**. Пароль не требуется (и его нельзя ввести), так как для подключения используются существующие учетные данные.

    ![Выбор встроенной аутентификации Active Directory][11]

2. Нажмите кнопку **Параметры**, а затем на странице **Свойства подключения** в поле **Подключение к базе данных** введите имя пользовательской базы данных для подключения. (Параметр **Доменное имя AD или идентификатор клиента** поддерживается только для параметра **Universal with MFA connection** (Универсальная с подключением MFA), в противном случае он неактивен.)  

    ![Выбор имени базы данных][13]

## <a name="active-directory-password-authentication"></a>Проверка пароля Active Directory

Используйте этот метод при подключении с помощью имени участника Azure AD, в котором используется управляемый домен Azure AD. Его также можно использовать для федеративных учетных записей без доступа к домену, например при удаленной работе.

Используйте этот способ для проверки подлинности в базе данных или хранилище данных SQL с Azure AD для собственных федеративных пользователей Azure AD. Собственные пользователи являются единственным исключением, созданным в Azure AD, для которых производится проверка подлинности с помощью имени и пароля, в то время как федеративный пользователь является пользователем Windows, чей домен объединен с Azure AD. Второй способ (использование пользователя и пароля) можно использовать, когда пользователю необходимо войти с учетными данными Windows, но локальный компьютер не присоединен к домену (при использовании, например, удаленного доступа). В этом случае пользователь Windows может указать учетную запись и пароль домена и пройти проверку подлинности в базе данных или хранилище данных SQL с помощью федеративных учетных данных.

1. Запустите Management Studio или Data Tools и в диалоговом окне **Подключение к серверу** (или **Подключиться к ядру СУБД**) в поле **Проверка подлинности** выберите **Active Directory — пароль**.

2. В поле **имя пользователя** введите имя пользователя Azure Active Directory в формате **имя пользователя\@domain.com**. Именем пользователя должна быть учетная запись из Azure Active Directory или учетная запись из федерации домена с Azure Active Directory.

3. В поле **Пароль** введите пароль пользователя для учетной записи Azure Active Directory или учетной записи федеративного домена.

    ![Выбор проверки пароля Active Directory][12]

4. Нажмите кнопку **Параметры**, а затем на странице **Свойства подключения** в поле **Подключение к базе данных** введите имя пользовательской базы данных для подключения. (См. рисунок в предыдущем варианте.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Использование удостоверения Azure AD для подключения из клиентского приложения

Ниже описаны действия по подключению к базе данных SQL с помощью удостоверения Azure AD из клиентского приложения.

### <a name="active-directory-integrated-authentication"></a>Встроенная аутентификация Active Directory

Для использования встроенной проверки подлинности Windows необходимо включить в федерацию Active Directory вашего домена и Azure Active Directory. Клиентское приложение (или служба), подключающееся к базе данных, должно быть запущено на компьютере, присоединенном к домену, с учетными данными пользователя домена.

Чтобы подключиться к базе данных с помощью встроенной проверки подлинности и удостоверения Azure AD, ключевое слово проверки подлинности в строке подключения к базе данных должно иметь значение «Active Directory Integrated». В следующем примере кода C# используется ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Ключевое слово `Integrated Security=True` строки подключения не используется для подключения к базе данных SQL Azure. При создании подключения ODBC потребуется удалить пробелы и задать для параметра Authentication значение ActiveDirectoryIntegrated.

### <a name="active-directory-password-authentication"></a>Проверка пароля Active Directory

Чтобы подключиться к базе данных с помощью встроенной аутентификации и удостоверения Azure AD, ключевому слову аутентификации нужно присвоить пароль Active Directory. Строка подключения должна содержать ключевые слова для идентификатора пользователя (UID) и пароля (PWD), а также их значения. В следующем примере кода C# используется ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Дополнительные сведения о методах проверки подлинности Azure AD и примеры кода вы найдете в [демонстрации проверки подлинности Azure AD на GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Токен Azure AD

Этот метод проверки подлинности позволяет службам среднего уровня подключаться к базе данных SQL Azure или хранилищу данных SQL Azure путем получения маркера из Azure Active Directory (AAD). Он используется для реализации сложных сценариев, включая проверку подлинности на основе сертификатов. Для работы с проверкой подлинности маркера Azure AD необходимо выполнить четыре основных шага.

1. Зарегистрируйте приложение в Azure Active Directory и получите идентификатор клиента для своего кода.
2. Создайте пользователя базы данных, представляющего приложение. (Выполнено ранее на шаге 6.)
3. Создайте сертификат на клиентском компьютере, на котором выполняется приложение.
4. Добавьте сертификат в качестве ключа для вашего приложения.

Пример строки подключения.

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Дополнительные сведения см. в [блоге о безопасности SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Дополнительные сведения о добавлении сертификата см. в статье [Приступая к работе с аутентификацией на основе сертификата в Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Ниже приведены инструкции для подключения с помощью sqlcmd версии 13.1, доступной в [Центре загрузки](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` с командой `-G` не работает с удостоверениями системы и требует входа субъекта-пользователя.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Устранение неполадок аутентификация Azure AD

Рекомендации по устранению неполадок, связанных с аутентификация Azure AD, можно найти в следующем блоге: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о доступе к базе данных SQL и управлении ею см. в статье [Контроль доступа к базе данных SQL Azure](sql-database-control-access.md).
- Общие сведения об именах для входа, пользователях и ролях базы данных в базе данных SQL см. в статье [Предоставление доступа к базе данных и управление им](sql-database-manage-logins.md).
- Дополнительные сведения о субъектах базы данных см. в [этой статье](https://msdn.microsoft.com/library/ms181127.aspx).
- Дополнительные сведения о ролях баз данных см. в статье [Роли уровня базы данных](https://msdn.microsoft.com/library/ms189121.aspx).
- Дополнительные сведения о правилах брандмауэра см. в статье [Обзор правил брандмауэра базы данных SQL Azure](sql-database-firewall-configure.md).

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
