---
title: Azure весны CI/CD в облаке с действиями GitHub
description: Создание рабочего процесса CI/CD для Azure Веснного облака с помощью действий GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 303f24ef6d934c0382bd8917833e3ec545f2a540
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776486"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure весны CI/CD в облаке с действиями GitHub

Действия GitHub поддерживают автоматизированный рабочий процесс жизненного цикла разработки программного обеспечения. С помощью действий GitHub для Azure "пружинное облако" вы можете создавать рабочие процессы в репозитории для создания, тестирования, упаковки, выпуска и развертывания в Azure. 

## <a name="prerequisites"></a>Технические условия
Для этого примера требуется [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>Настройка репозитория GitHub и проверка подлинности
Для авторизации действия входа в Azure требуются учетные данные участника службы Azure. Чтобы получить учетные данные Azure, выполните следующие команды на локальном компьютере:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Для доступа к определенной группе ресурсов можно уменьшить область:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Команда должна вывести объект JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

В этом примере используется пример [метрик свинка](https://github.com/Azure-Samples/piggymetrics) на GitHub.  Разветвление образца, откройте страницу репозитория GitHub и щелкните вкладку **Параметры** . Откройте меню **секреты** и выберите команду **Добавить новый секрет**:

 ![Добавить новый секрет](./media/github-actions/actions1.png)

Задайте в качестве имени секрета `AZURE_CREDENTIALS` и его значение в строке JSON, которую вы нашли в заголовке, *настройте репозиторий GitHub и пройдите проверку подлинности*.

 ![Настройка секретных данных](./media/github-actions/actions2.png)

Вы также можете получить учетные данные для входа в Azure из Key Vault в действиях GitHub, как описано в статьях [Проверка подлинности Azure с помощью Key Vault в действиях GitHub](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Инициализация экземпляра службы
Для подготовки экземпляра облачной службы Azure весны выполните следующие команды с помощью Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Создание рабочего процесса
Рабочий процесс определяется с помощью следующих параметров.

### <a name="prepare-for-deployment-with-azure-cli"></a>Подготовка к развертыванию с помощью Azure CLI
Команда `az spring-cloud app create` в настоящее время не идемпотентными.  Этот рабочий процесс рекомендуется для существующих облачных приложений и экземпляров Azure весны.

Для подготовки используйте следующие Azure CLI команды:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Прямое развертывание с помощью Azure CLI
Создайте файл `.github/workflow/main.yml` в репозитории:

```
name: AzureSpringCloud

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Развертывание с действием Azure CLI
Команда AZ `run` будет использовать последнюю версию Azure CLI. При наличии критических изменений можно также использовать определенную версию Azure CLI с `action`Azure/CLI. 

> [!Note] 
> Эта команда будет выполняться в новом контейнере, поэтому `env` не будет работать, и доступ к файлам для кросс-действий может иметь дополнительные ограничения.

Создайте файл. GitHub/Workflow/Main. yml в репозитории:
```
name: AzureSpringCloud

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Развертывание с помощью подключаемого модуля Maven
Другой вариант — использовать [подключаемый модуль Maven](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) для развертывания JAR-файла и обновления параметров приложения. Команда `mvn azure-spring-cloud:deploy` идемпотентными и автоматически создаст приложения, если это необходимо. Создавать соответствующие приложения заранее не требуется.

```
name: AzureSpringCloud

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>Запуск рабочего процесса
**Действия** GitHub должны быть включены автоматически после отправки `.github/workflow/main.yml` в GitHub. Действие будет активировано при отправке новой фиксации. При создании этого файла в браузере действие должно быть уже запущено.

Чтобы убедиться, что действие включено, щелкните вкладку **действия** на странице репозитория GitHub:

 ![Проверка включения действия](./media/github-actions/actions3.png)

Если действие выполняется в случае ошибки, например, если вы не задали учетные данные Azure, вы можете повторно выполнить проверку после исправления ошибки. На странице репозиторий GitHub щелкните **действия**, выберите конкретную задачу рабочего процесса, а затем нажмите кнопку **Повторное** выполнение проверок, чтобы повторно запустить проверки:

 ![Повторить проверку](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Key Vault для действий в облаке GitHub](./spring-cloud-github-actions-key-vault.md)
* [Субъекты-службы Azure Active Directory](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Действия GitHub для Azure](https://github.com/Azure/actions/)
