---
title: Руководство. Подготовка инфраструктуры со слотами развертывания Azure с помощью Terraform
description: Руководство по использованию слотов развертывания поставщика Terraform с Azure
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 68c790b4fad442d94e6ac82d1a545b8554d2dd4f
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159177"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Руководство по подготовке инфраструктуры со слотами развертывания Azure с помощью Terraform

Вы можете использовать [слоты развертывания Azure](/azure/app-service/deploy-staging-slots) для переключения между разными версиями приложения. Эту возможность помогает минимизировать влияние неработающих развертываний. 

В этой статье показан пример использования слотов развертывания. Мы рассмотрим развертывание двух приложений через GitHub и Azure. Одно из приложений размещается в рабочем слоте, а второе — в промежуточном. (Имена "production" и "staging" являются произвольными. Это могут быть любые имена, которые подходят для вашего сценария.) После настройки слотов развертывания используйте Terraform для переключения между двумя слотами.

## <a name="prerequisites"></a>Предварительные требования

- **Подписка Azure.** Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , прежде чем начинать работу.

- **Учетная запись GitHub**. Учетная запись [GitHub](https://www.github.com) необходима для создания вилки и использования тестового репозитория GitHub.

## <a name="create-and-apply-the-terraform-plan"></a>Создание и применение плана Terraform

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview). Если ранее среда не была выбрана, то в качестве среды необходимо выбрать **Bash**.

    ![Командная строка Cloud Shell](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Создайте каталог с именем `deploy`.

    ```bash
    mkdir deploy
    ```

1. Создайте каталог с именем `swap`.

    ```bash
    mkdir swap
    ```

1. Используйте команду bash `ls`, чтобы проверить, успешно ли созданы оба каталога.

    ![Cloud Shell после создания каталогов](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Перейдите в каталог `deploy`.

    ```bash
    cd deploy
    ```

1. В Cloud Shell создайте файл с именем `deploy.tf`.

    ```bash
    code deploy.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

1. После создания файла проверьте его содержимое.

    ```bash
    cat deploy.tf
    ```

1. Инициализируйте Terraform.

    ```bash
    terraform init
    ```

1. Создайте план Terraform.

    ```bash
    terraform plan
    ```

1. Подготовьте ресурсы, определенные в файле конфигурации `deploy.tf`. (Чтобы подтвердить действие, введите `yes` в ответ на запрос.)

    ```bash
    terraform apply
    ```

1. Закройте окно Cloud Shell.

1. В главном меню на портале Azure выберите **Группы ресурсов**.

    ![Выбор "Группы ресурсов" на портале](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. На вкладке **Группы ресурсов** выберите **slotDemoResourceGroup**.

    ![Группа ресурсов, созданная в Terraform.](./media/terraform-slot-walkthru/resource-group.png)

Вы должны увидеть все ресурсы, созданные Terraform.

![Ресурсы, созданные в Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Разветвление тестового проекта

Прежде чем начать тестирование создания и переключения слотов развертывания, необходимо создать разветвление тестового проекта на сайте GitHub.

1. Перейдите к [репозиторию awesome-terraform на GitHub](https://github.com/Azure/awesome-terraform).

1. Выполните разветвление репозитория **awesome-terraform**.

    ![Разветвление репозитория GitHub awesome-terraform](./media/terraform-slot-walkthru/fork-repo.png)

1. Выполните все запрашиваемые действия для создания разветвления в среде.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Развертывание из GitHub в слоты развертывания

После разветвления репозитория тестового проекта настройте слоты развертывания, выполнив следующие действия:

1. В главном меню на портале Azure выберите **Группы ресурсов**.

1. Выберите **slotDemoResourceGroup**.

1. Выберите **slotAppService**.

1. Щелкните пункт **Варианты развертывания**.

    ![Варианты развертывания для ресурса службы приложений](./media/terraform-slot-walkthru/deployment-options.png)

1. На вкладке **Вариант развертывания** выберите **Выбор источника**, а затем щелкните **GitHub**.

    ![Выбор источника развертывания](./media/terraform-slot-walkthru/select-source.png)

1. Когда Azure установит подключение и отобразятся все параметры, выберите **Авторизация**.

1. На вкладке **Авторизация** выберите **Авторизовать** и укажите учетные данные, необходимые Azure для получения доступа к вашей учетной записи GitHub. 

1. После того как Azure проверит учетные данные GitHub, появится сообщение, указывающее на то, что процесс авторизации завершен. Нажмите кнопку **ОК**, чтобы закрыть вкладку **Авторизация**.

1. Щелкните **Choose your organization** (Выбор организации) и выберите вашу организацию.

1. Щелкните **Выберите проект**.

1. На вкладке **Выбор проекта** щелкните проект **awesome-terraform**.

    ![Выбор проекта awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Щелкните **Выберите ветвь**.

1. На вкладке **Выбор ветви** щелкните **master** (главная).

    ![Выбор главной ветви](./media/terraform-slot-walkthru/choose-branch-master.png)

1. На вкладке **Вариант развертывания** нажмите кнопку **ОК**.

На этом этапе выполнено развертывание рабочего слота. Чтобы развернуть промежуточный слот, выполните предыдущие действия, используя следующие изменения:

- На шаге 3 выберите ресурс **slotAppServiceSlotOne**.

- На шаге 13 выберите рабочую ветвь вместо главной.

    ![Выбор рабочей ветви](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Тестирование развертываний приложения

В предыдущих разделах вы настроили два слота (**slotAppService** и **slotAppServiceSlotOne**), чтобы выполнять развертывание из разных ветвей в GitHub. Чтобы проверить, что веб-приложения были успешно развернуты, их нужно предварительно просмотреть.

1. В главном меню на портале Azure выберите **Группы ресурсов**.

1. Выберите **slotDemoResourceGroup**.

1. Выберите **slotAppService** или **slotAppServiceSlotOne**.

1. На странице обзора выберите **URL-адрес**.

    ![Выбор URL-адреса на вкладке обзора для отображения приложения](./media/terraform-slot-walkthru/resource-url.png)

1. В зависимости от выбранного приложения, вы увидите следующие результаты:
    - Веб-приложение **slotAppService** — синяя страница с заголовком **Slot Demo App 1** (Демонстрационное приложение слота 1). 
    - Веб-приложение **slotAppServiceSlotOne** — синяя страница с заголовком **Slot Demo App 2** (Демонстрационное приложение слота 2).

    ![Предварительный просмотр приложений для проверки правильного развертывания](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Переключение между двумя слотами развертывания

Чтобы протестировать переключение между двумя слотами развертывания, сделайте следующее:
 
1. Перейдите на вкладку браузера, на которой выполняется **slotAppService** (приложение с синей страницей). 

1. Откройте портал Azure в отдельной вкладке.

1. Откройте Cloud Shell.

1. Перейдите в каталог **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. В Cloud Shell создайте файл с именем `swap.tf`.

    ```bash
    code swap.tf
    ```

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Сохраните файл ( **&lt;Ctrl>S**) и закройте редактор ( **&lt;Ctrl>Q**).

1. Инициализируйте Terraform.

    ```bash
    terraform init
    ```

1. Создайте план Terraform.

    ```bash
    terraform plan
    ```

1. Подготовьте ресурсы, определенные в файле конфигурации `swap.tf`. (Чтобы подтвердить действие, введите `yes` в ответ на запрос.)

    ```bash
    terraform apply
    ```

1. Вернитесь в браузер после переключения Terraform между слотами. Обновите страницу. 

Веб-приложение в промежуточном слоте **slotAppServiceSlotOne** было переведено на рабочий слот и теперь отображается зеленым цветом. 

![Слоты развертывания переключены](./media/terraform-slot-walkthru/slots-swapped.png)

Чтобы вернуться к исходной рабочей версии приложения, повторно примените план Terraform, созданный вами с помощью файла конфигурации `swap.tf`.

```bash
terraform apply
```

После переключения приложения отобразится исходная конфигурация.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"] 
> [Документация по Terraform в Azure](/azure/terraform)