---
title: Задача DevOps Azure для Azure обозреватель данных
description: В этом разделе вы узнаете, как создать конвейер выпуска и развернуть его.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 6394d7149bd4e80f0a17a59a6259eedf4c806fd4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188182"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Задача DevOps Azure для Azure обозреватель данных

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) предоставляет средства для совместной работы по разработке, такие как высокопроизводительные конвейеры, бесплатные закрытые репозитории Git, настраиваемые доски Канбан и широкие возможности автоматического и постоянного тестирования. [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/) — это функция Azure DevOps, которая позволяет управлять CI/CD для развертывания кода с помощью высокопроизводительных конвейеров, работающих с любым языком, платформой и облаком.
[Команды Azure обозреватель данных-Admin](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) — это Azure pipelines задача, позволяющая создавать конвейеры выпуска и развертывать изменения базы данных в базах данных Azure обозреватель данных. Он доступен бесплатно в [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

В этом документе описывается простой пример использования задачи **Azure обозреватель данных – Admin Commands** для развертывания изменений схемы в базе данных. Полный конвейер CI/CD см. в [документации по Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Настройка кластера Azure обозреватель данных:
    * [Кластер и база данных Azure обозреватель данных](/azure/data-explorer/create-cluster-database-portal)
    * Создайте приложение Azure Active Directory (Azure AD), выполнив [подготовку приложения Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Предоставьте доступ к Azure AD App в базе данных Azure обозреватель данных, [управляя разрешениями на базе данных azure обозреватель данных](/azure/data-explorer/manage-database-permissions).
* Настройка Azure DevOps:
    * [Регистрация в бесплатной Организации](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Создание Организации](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Создание проекта в Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Код с помощью Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Создание папок

Создайте в репозитории Git следующие образцы папок (*функций*, *политик*, *таблиц*). Скопируйте файлы [отсюда](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) в соответствующие папки, как показано ниже, и зафиксируйте изменения. Примеры файлов предоставляются для выполнения следующего рабочего процесса.

![Создание папок](media/devops/create-folders.png)

> [!TIP]
> При создании собственного рабочего процесса рекомендуется сделать код идемпотентными. Например, используйте инструкцию [. CREATE-merge Table](/azure/kusto/management/create-table-command#create-merge-table) вместо [. CREATE TABLE](/azure/kusto/management/create-table-command)и используйте [инструкцию. Create-или-ALTER](/azure/kusto/management/functions#create-or-alter-function) Function вместо [. Create](/azure/kusto/management/functions#create-function) .

## <a name="create-a-release-pipeline"></a>Создание конвейера выпуска

1. Войдите в [организацию Azure DevOps](https://dev.azure.com/).
1. Выберите **конвейеры** > **выпуски** в левой части меню и выберите **создать конвейер**.

    ![Новый конвейер](media/devops/new-pipeline.png)

1. Откроется окно **Новый конвейер выпуска** . На вкладке **конвейеры** на панели **Выбор шаблона** выберите **пустое задание**.

     ![Выберите шаблон](media/devops/select-template.png)

1. Нажмите кнопку **этап** . В области **стадия** добавьте **имя этапа**. Нажмите кнопку **сохранить** , чтобы сохранить конвейер.

    ![Имя этапа](media/devops/stage-name.png)

1. Нажмите кнопку **Добавить артефакт** . В области **Добавить артефакт** выберите репозиторий, в котором находится ваш код, введите нужные сведения и нажмите кнопку **Добавить**. Нажмите кнопку **сохранить** , чтобы сохранить конвейер.

    ![Добавление артефакта](media/devops/add-artifact.png)

1. На вкладке **переменные** выберите **+ Добавить** , чтобы создать переменную для **URL-адреса конечной точки** , которая будет использоваться в задаче. Запишите **имя** и **значение** конечной точки. Нажмите кнопку **сохранить** , чтобы сохранить конвейер. 

    ![Создать переменную](media/devops/create-variable.png)

    Чтобы найти Endpoint_URL, страница обзора **кластера azure обозреватель данных** в портал Azure содержит URI кластера Azure обозреватель данных. Создайте URI в следующем формате `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Например, HTTPS:\//кустодокс.вестус.Кусто.Виндовс.нет? DatabaseName = SampleDB

    ![Универсальный код ресурса кластера Azure обозреватель данных](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Создание задач для развертывания

1. На вкладке **конвейер** щелкните **1 задание, 0 задачу,** чтобы добавить задачи. 

    ![Добавление задач](media/devops/add-task.png)

1. Создайте три задачи для развертывания **таблиц**, **функций**и **политик**в указанном порядке. 

1. На вкладке **задачи** выберите **+** по **заданию агента**. Найдите **Azure Data Explorer**. В **Marketplace**установите расширение **"команды Azure обозреватель данных — администратор"** . Затем выберите **Добавить** в **Run Azure обозреватель данных команда**.

     ![Добавление команд администратора](media/devops/add-admin-commands.png)

1. Щелкните **команду Kusto** слева и обновите задачу, указав следующие сведения:
    * **Отображаемое имя**: имя задачи
    * **Путь к файлу**: в задаче **таблицы** укажите */Tables/* . CSL, так как файлы создания таблиц находятся в папке *Table* .
    * **URL-адрес конечной точки**: Введите переменную `EndPoint URL`, созданную на предыдущем шаге.
    * Выберите **использовать конечную точку службы** и нажмите кнопку **+ создать**.

    ![Задача "обновление команды Kusto"](media/devops/kusto-command-task.png)

1. В окне **Добавление подключения к службе Обозреватель данных Azure** введите следующие сведения:

    |Параметр  |Рекомендуемое значение  |
    |---------|---------|
    |**Имя соединения**     |    Введите имя для указания этой конечной точки службы     |
    |**URL-адрес кластера**    |    Значение можно найти в разделе Обзор кластера Azure обозреватель данных в портал Azure | 
    |**Идентификатор субъекта-службы**    |    Введите идентификатор приложения AAD (созданный в качестве необходимого компонента)     |
    |**Ключ приложения субъекта-службы**     |    Введите ключ приложения AAD (созданный в качестве необходимого компонента)    |
    |**Идентификатор клиента AAD**    |      Введите клиент AAD (например, microsoft.com, contoso.com...).    |

    Установите флажок **Разрешить всем конвейерам использовать это подключение** . Нажмите кнопку **ОК**.

    ![Добавить подключение к службе](media/devops/add-service-connection.png)

1. Повторите шаги 1-5 еще два раза, чтобы развернуть файлы из папок *функции* и *политики* . Нажмите кнопку **Сохранить**. На вкладке **задачи** см. три созданные задачи: **Развертывание таблиц**, **развертывание функций**и **развертывание политик**.

    ![Развернуть все папки](media/devops/deploy-all-folders.png)

1. Выберите **+ release** > **создать выпуск** , чтобы создать выпуск.

    ![Создание выпуска](media/devops/create-release.png)

1. На вкладке **журналы** Проверьте состояние развертывания успешно.

    ![Развертывание успешно завершено](media/devops/deployment-successful.png)

Теперь вы завершили создание конвейера выпуска для развертывания трех задач в подготовительной среде.