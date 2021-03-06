---
title: Корпоративная безопасность
titleSuffix: Azure Machine Learning
description: 'Безопасно используйте Машинное обучение Azure: Аутентификация, авторизация, безопасность сети, шифрование данных и мониторинг.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/09/2020
ms.openlocfilehash: bc083a95ebf6c7ecfabfef87e606f99053ba58bb
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312419"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Корпоративная безопасность для Машинное обучение Azure

В этой статье вы узнаете о функциях безопасности, доступных для Машинное обучение Azure.

При использовании облачной службы рекомендуется ограничить доступ только тем пользователям, которым она нужна. Начните с понимания модели проверки подлинности и авторизации, используемой службой. Вам также может потребоваться ограничить сетевой доступ или безопасно присоединять ресурсы в локальной сети с облаком. Шифрование данных также крайне важно, как при хранении, так и при перемещении данных между службами. Наконец, необходимо иметь возможность отслеживать службу и создавать журнал аудита всех действий.

> [!NOTE]
> Сведения в этой статье работают с пакетом SDK для Машинное обучение Azure Python версии 1.0.83.1 или более поздней.

## <a name="authentication"></a>Проверка подлинности

Многофакторная проверка подлинности поддерживается, если Azure Active Directory (Azure AD) настроен для использования. Ниже приведен процесс проверки подлинности.

1. Клиент входит в Azure AD и получает маркер Azure Resource Manager.  Пользователи и субъекты-службы поддерживаются полностью.
1. Клиент предоставляет маркер для Azure Resource Manager и для всех Машинное обучение Azure.
1. Служба Машинное обучение предоставляет маркер службы Машинное обучение для целевого объекта вычислений пользователя (например, Вычислительная среда Машинного обучения). Этот маркер используется целевым объектом вычислений пользователя для обратного вызова в службу Машинное обучение после завершения выполнения. Область ограничена рабочей областью.

[![проверки подлинности в Машинное обучение Azure](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Дополнительные сведения см. в разделе [Настройка проверки подлинности для машинное обучение Azure ресурсов и рабочих процессов](how-to-setup-authentication.md). В этой статье содержатся сведения и примеры проверки подлинности, включая использование субъектов-служб и автоматических рабочих процессов.


### <a name="authentication-for-web-service-deployment"></a>Проверка подлинности для развертывания веб-службы

Машинное обучение Azure поддерживает две формы проверки подлинности для веб-служб: ключ и маркер. Каждая веб-служба в каждый момент времени может включать только одну форму проверки подлинности.

|Метод проверки подлинности|Description|Служба "Экземпляры контейнеров Azure"|AKS|
|---|---|---|---|
|Ключ|Ключи являются статическими и не нуждаются в обновлении. Ключи можно создать повторно вручную.|По умолчанию отключено| По умолчанию включено|
|Токен|Срок действия токенов истекает через указанный период времени, и его необходимо обновить.| Недоступно| По умолчанию отключено |

Примеры кода см. в [разделе Проверка подлинности веб-служб](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Авторизация

Вы можете создать несколько рабочих областей, и каждая рабочая область может совместно использоваться несколькими пользователями. Когда вы предоставляете общий доступ к рабочей области, вы можете управлять доступом к ней, назначая пользователям следующие роли:

* Владелец
* Участник
* Читатель

В следующей таблице перечислены некоторые основные операции Машинное обучение Azure и роли, которые могут их выполнять.

| Машинное обучение Azure операция | Владелец | Участник | Читатель |
| ---- |:----:|:----:|:----:|
| Создание рабочей области | ✓ | ✓ | |
| Общая рабочая область | ✓ | |  |
| Обновление рабочей области до выпуска Enterprise Edition | ✓ | |
| Создание целевого объекта вычислений | ✓ | ✓ | |
| Присоединить целевой объект вычислений | ✓ | ✓ | |
| Подключение хранилищ данных | ✓ | ✓ | |
| Выполнение эксперимента | ✓ | ✓ | |
| Просмотр запусков и метрик | ✓ | ✓ | ✓ |
| Регистрация модели | ✓ | ✓ | |
| Создание образа | ✓ | ✓ | |
| Развертывание веб-службы | ✓ | ✓ | |
| Просмотр моделей и изображений | ✓ | ✓ | ✓ |
| Вызвать веб-службу | ✓ | ✓ | ✓ |

Если встроенные роли не соответствуют вашим потребностям, можно создать пользовательские роли. Пользовательские роли поддерживаются только для операций в рабочей области и Вычислительная среда Машинного обучения. Пользовательские роли могут иметь разрешения на чтение, запись или удаление в рабочей области и на ресурсе вычислений в этой рабочей области. Роль можно сделать доступной на определенном уровне рабочей области, определенном уровне группы ресурсов или определенном уровне подписки. Дополнительные сведения см. [в статье Manage Users and Roles in a машинное обучение Azure Workspace](how-to-assign-roles.md).

### <a name="securing-compute-targets-and-data"></a>Обеспечение безопасности целевых объектов вычислений и данных

Владельцы и участники могут использовать все целевые объекты вычислений и хранилища данных, подключенные к рабочей области.  

Каждая Рабочая область также имеет связанное с системой управляемое удостоверение, которое имеет то же имя, что и Рабочая область. Управляемое удостоверение имеет следующие разрешения для подключенных ресурсов, используемых в рабочей области.

Дополнительные сведения об управляемых удостоверениях см. в статье [управляемые удостоверения для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Ресурс | Разрешения |
| ----- | ----- |
| Рабочая область | Участник |
| Учетная запись хранения | Участник данных BLOB-объекта хранилища |
| Key Vault; | Доступ ко всем ключам, секретам, сертификатам |
| Реестр контейнеров Azure | Участник |
| Группа ресурсов, содержащая рабочую область | Участник |
| Группа ресурсов, содержащая хранилище ключей (если оно отличается от того, в котором находится рабочая область) | Участник |

Не рекомендуется, чтобы администраторы отменяют доступ управляемого удостоверения к ресурсам, упомянутым в предыдущей таблице. Вы можете восстановить доступ с помощью операции повторной синхронизации ключей.

Машинное обучение Azure создает дополнительное приложение (имя начинается с `aml-` или `Microsoft-AzureML-Support-App-`) с доступом на уровне участника в подписке для каждого региона рабочей области. Например, если у вас есть одна рабочая область в восточной части США и одна в Северной Европе в той же подписке, вы увидите два из этих приложений. Эти приложения позволяют Машинное обучение Azure, помогающие управлять ресурсами вычислений.

## <a name="network-security"></a>Безопасность сети

Машинное обучение Azure полагается на другие службы Azure для ресурсов вычислений. Вычислительные ресурсы (целевые объекты вычислений) используются для обучения и развертывания моделей. Эти целевые объекты вычислений можно создать в виртуальной сети. Например, можно использовать виртуальную машину Azure для обработки и анализа данных, чтобы обучить модель, а затем развернуть ее в AKS.  

Дополнительные сведения см. в разделе [как выполнять эксперименты и вывод в виртуальной сети](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>шифрование данных;

### <a name="encryption-at-rest"></a>Шифрование при хранении

> [!IMPORTANT]
> Если Рабочая область содержит конфиденциальные данные, при создании рабочей области рекомендуется установить [флаг hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) . Это позволяет управлять объемом данных, собираемых корпорацией Майкрософт в целях диагностики, и обеспечивает дополнительное шифрование в управляемых средах Майкрософт.


#### <a name="azure-blob-storage"></a>Хранилище BLOB-объектов Azure

Машинное обучение Azure сохраняет моментальные снимки, выходные данные и журналы в учетной записи хранилища BLOB-объектов Azure, привязанной к рабочей области Машинное обучение Azure и вашей подписке. Все данные, хранящиеся в хранилище BLOB-объектов Azure, шифруются при хранении с помощью ключей, управляемых корпорацией Майкрософт.

Сведения о том, как использовать собственные ключи для хранения данных, хранящихся в хранилище BLOB-объектов Azure, см. [в разделе Шифрование службы хранилища Azure с помощью ключей, управляемых клиентом, в Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Обучающие данные, как правило, также хранятся в хранилище BLOB-объектов Azure, чтобы они были доступны для обучения целевых объектов вычислений. Это хранилище не управляется Машинное обучение Azure но подключено к целевым объектам вычислений как удаленную файловую систему.

Дополнительные сведения о повторном создании ключей доступа см. в разделе [Повторное создание ключей доступа к хранилищу](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Машинное обучение Azure сохраняет метрики и метаданные в экземпляре Azure Cosmos DB. Этот экземпляр связан с подпиской Майкрософт, управляемой Машинное обучение Azure. Все данные, хранящиеся в Azure Cosmos DB, шифруются при хранении с помощью ключей, управляемых корпорацией Майкрософт.

Чтобы использовать собственные ключи (управляемые пользователем) для шифрования экземпляра Azure Cosmos DB, можно создать выделенный экземпляр Cosmos DB для использования с рабочей областью. Рекомендуется использовать этот подход, если вы хотите хранить данные, например данные журнала выполнения, за пределами многоклиентского Cosmos DB экземпляра, размещенного в нашей подписке Майкрософт. 

> [!NOTE]
> В настоящее время эта функция доступна только в восточной части США, Западная часть США 2, Юго-Центральный регион США.

Чтобы включить подготовку Cosmos DB экземпляра в подписке с помощью управляемых клиентом ключей, выполните следующие действия.

* Включите возможности управления ключами, управляемыми клиентом, для Cosmos DB. В настоящее время необходимо запросить доступ, чтобы использовать эту возможность. Для этого обратитесь в [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

* Зарегистрируйте поставщиков ресурсов Машинное обучение Azure и Azure Cosmos DB в подписке, если это еще не сделано.

* Авторизовать приложение Машинное обучение (в службе управления удостоверениями и доступом) с разрешениями участника в вашей подписке.

    ![Авторизация "Машинное обучение Azure приложения" в управлении удостоверениями и доступом на портале](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* При создании рабочей области Машинное обучение Azure используйте следующие параметры. Оба параметра являются обязательными и поддерживаются в пакетах SDK, CLI, интерфейсах API и диспетчер ресурсов.

    * `resource_cmk_uri`. Этот параметр является полным URI ресурса для ключа, управляемого клиентом, в хранилище ключей, включая [сведения о версии для ключа](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`. Этот параметр является ИДЕНТИФИКАТОРом ресурса хранилища ключей в вашей подписке. Это хранилище ключей должно находиться в том же регионе и подписке, которые будут использоваться для рабочей области Машинное обучение Azure. 
    
        > [!NOTE]
        > Этот экземпляр хранилища ключей может отличаться от хранилища ключей, созданного Машинное обучение Azure при подготовке рабочей области. Если вы хотите использовать один и тот же экземпляр хранилища ключей для рабочей области, передайте это же хранилище ключей при подготовке рабочей области с помощью [параметра key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Этот Cosmos DB экземпляр создается в группе ресурсов, управляемой корпорацией Майкрософт в вашей подписке. 

> [!IMPORTANT]
> * Если необходимо удалить этот Cosmos DB экземпляр, необходимо удалить рабочую область Машинное обучение Azure, которая его использует. 
> * [__Единицы запроса__](../cosmos-db/request-units.md) по умолчанию для этой учетной записи Cosmos DB задаются в __8000__. Изменение этого значения не поддерживается. 

Дополнительные сведения о ключах, управляемых клиентом, с помощью Cosmos DB см. в статье [Настройка ключей, управляемых клиентом, для учетной записи Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Реестр контейнеров Azure

Все образы контейнеров в реестре (реестр контейнеров Azure) шифруются при хранении. Azure автоматически шифрует образ перед сохранением и расшифровывает его, когда Машинное обучение Azure извлекает образ.

Чтобы использовать собственные ключи (управляемые пользователем) для шифрования реестра контейнеров Azure, необходимо создать собственную запись контроля доступа и подключить ее при подготовке рабочей области или шифровать экземпляр по умолчанию, который создается во время подготовки рабочей области.

Пример создания рабочей области с помощью существующего реестра контейнеров Azure см. в следующих статьях:

* [Создайте рабочую область для машинное обучение Azure с Azure CLI](how-to-manage-workspace-cli.md).
* [Создание рабочей области для Машинное обучение Azure с помощью шаблона Azure Resource Manager](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Экземпляр контейнера Azure

Экземпляр контейнера Azure не поддерживает шифрование дисков. Если требуется шифрование дисков, рекомендуется [выполнить развертывание в экземпляре службы Kubernetes Azure](how-to-deploy-azure-kubernetes-service.md) . В этом случае вы также можете использовать поддержку Машинное обучение Azure для управления доступом на основе ролей, чтобы предотвратить развертывание в экземпляре контейнера Azure в вашей подписке.

#### <a name="azure-kubernetes-service"></a>Служба Azure Kubernetes

Развернутый ресурс службы Azure Kubernetes можно зашифровать с помощью ключей, управляемых клиентом, в любое время. Дополнительные сведения см. в статье [перевод собственных ключей в службу Azure Kubernetes](../aks/azure-disk-customer-managed-keys.md). 

Этот процесс позволяет шифровать данные и диск операционной системы развернутых виртуальных машин в кластере Kubernetes.

> [!IMPORTANT]
> Этот процесс работает только с AKS K8s версии 1,17 или более поздней. Машинное обучение Azure добавлена поддержка AKS 1,17 на 13 января 2020 г.

#### <a name="machine-learning-compute"></a>Вычислительная среда Машинного обучения

Диск операционной системы для каждого расчетного узла, хранящегося в службе хранилища Azure, шифруется с помощью ключей, управляемых корпорацией Майкрософт, в Машинное обучение Azure учетных записях хранения. Этот целевой объект вычислений является эфемерным, и кластеры, как правило, масштабируются вниз, если в очереди нет запусков. Базовая виртуальная машина не подготавливается, и диск ОС удаляется. Шифрование дисков Azure не поддерживается для диска ОС.

Каждая виртуальная машина также имеет локальный временный диск для операций операционной системы. При необходимости можно использовать диск для размещения обучающих данных. По умолчанию диск шифруется для рабочих областей с параметром `hbi_workspace`, для которого задано значение `TRUE`. Эта среда кратковременно используется только в течение выполнения, а поддержка шифрования ограничена только ключами, управляемыми системой.

Дополнительные сведения о том, как работает шифрование при работе в Azure, см. в статье [Шифрование неактивных данных](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)в Azure.

### <a name="encryption-in-transit"></a>Шифрование при передаче

SSL можно использовать для защиты внутренней связи между Машинное обучение Azure микрослужбами и для защиты внешних вызовов конечной точки оценки. Все доступ к хранилищу Azure также осуществляется по безопасному каналу.

Дополнительные сведения см. [в статье Использование SSL для защиты веб-службы с помощью машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Использование Azure Key Vault

Машинное обучение Azure использует экземпляр Azure Key Vault, связанный с рабочей областью, для хранения учетных данных различных типов:

* Строка подключения связанной учетной записи хранения
* Пароли для экземпляров репозитория контейнеров Azure
* Строки подключения к хранилищам данных

Пароли и ключи SSH для целевых вычислений, таких как Azure HDInsight и виртуальные машины, хранятся в отдельном хранилище ключей, связанном с подпиской Майкрософт. Машинное обучение Azure не хранит пароли или ключи, предоставленные пользователями. Вместо этого он создает, авторизует и сохраняет собственные ключи SSH для подключения к виртуальным машинам и HDInsight для выполнения экспериментов.

С каждой рабочей областью связано управляемое системное удостоверение с тем же именем, что и у рабочей области. Это управляемое удостоверение имеет доступ ко всем ключам, секретам и сертификатам в хранилище ключей.

## <a name="data-collection-and-handling"></a>Сбор и обработка данных

### <a name="microsoft-collected-data"></a>Собранные данные Майкрософт

Корпорация Майкрософт может выполнять сбор данных, не являющихся пользователями, таких как имена ресурсов (например, имя набора данных или имя эксперимента машинного обучения) или переменные среды задания для диагностики. Все эти данные хранятся с помощью ключей, управляемых корпорацией Майкрософт, в хранилище, размещенном в подписках Майкрософт, и соответствует [стандартным политикам конфиденциальности и стандартам обработки данных корпорации Майкрософт](https://privacy.microsoft.com/privacystatement).

Корпорация Майкрософт также рекомендует не хранить конфиденциальные сведения (например, секретные ключи учетной записи) в переменных среды. Переменные среды записываются в журнал, шифруются и хранятся в США.

Вы можете отказаться от сбора диагностических данных, задав для параметра `hbi_workspace` значение `TRUE` при подготовке рабочей области. Эта функция поддерживается при использовании пакетов SDK для AzureML Python, интерфейса командной строки, API-интерфейсов RESTFUL или шаблонов Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Данные, созданные корпорацией Майкрософт

При использовании таких служб, как автоматизированные Машинное обучение, корпорация Майкрософт может создавать временные предварительно обработанные данные для обучения нескольких моделей. Эти данные хранятся в хранилище данных в рабочей области, что позволяет применять элементы управления доступом и шифрование соответствующим образом.

Кроме того, вы можете зашифровать [диагностические данные, записанные из развернутой конечной точки](how-to-enable-app-insights.md) , в экземпляр Application Insights Azure.

## <a name="monitoring"></a>Мониторинг

### <a name="metrics"></a>Метрики

Вы можете использовать метрики Azure Monitor, чтобы просматривать и отслеживать метрики для рабочей области Машинное обучение Azure. В [портал Azure](https://portal.azure.com)выберите рабочую область, а затем щелкните **метрики**:

[Снимок экрана ![с примерами метрик для рабочей области](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Метрики включают сведения о запусках, развертываниях и регистрациях.

Дополнительные сведения см. [в разделе метрики в Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Журнал действий

Вы можете просмотреть журнал действий рабочей области, чтобы просмотреть различные операции, выполняемые в рабочей области. Журнал содержит основные сведения, такие как имя операции, инициатор события и отметка времени.

На этом снимке экрана показан журнал действий рабочей области.

[Снимок экрана ![, показывающий журнал действий рабочей области](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Сведения о запросе оценки хранятся в Application Insights. Application Insights создается в подписке при создании рабочей области. Записанные в журнал сведения включают такие поля, как:

* HTTPMethod
* UserAgent
* компутетипе
* RequestUrl
* StatusCode
* RequestId
* Длительность

> [!IMPORTANT]
> Некоторые действия в рабочей области Машинное обучение Azure не заводят данные в журнал действий. Например, запуск обучающего запуска и регистрация модели не регистрируются.
>
> Некоторые из этих действий отображаются в области **действия** рабочей области, но эти уведомления не указывают, кто инициировал действие.

## <a name="data-flow-diagrams"></a>Схемы потоков данных

### <a name="create-workspace"></a>Создание рабочей области

На следующей схеме показан рабочий процесс создание рабочей области.

* Войдите в Azure AD с одного из поддерживаемых Машинное обучение Azure клиентов (Azure CLI, пакет SDK для Python портал Azure) и запросите соответствующий маркер Azure Resource Manager.
* Чтобы создать рабочую область, вызовите Azure Resource Manager. 
* Azure Resource Manager обращается к поставщику ресурсов Машинное обучение Azure, чтобы подготавливать рабочую область.

Во время создания рабочей области в подписке пользователя создаются дополнительные ресурсы:

* Key Vault (для хранения секретов)
* Учетная запись хранения Azure (включая большой двоичный объект и файловый ресурс)
* Реестр контейнеров Azure (для хранения образов DOCKER для определения/оценки и экспериментирования)
* Application Insights (для хранения данных телеметрии)

При необходимости пользователь может также подготавливать другие целевые объекты вычислений, подключенные к рабочей области (например, службу Kubernetes Azure или виртуальные машины).

[Рабочий процесс создания рабочей области ![](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Сохранить исходный код (обучающие сценарии)

На следующей диаграмме показан рабочий процесс моментального снимка кода.

Связанная с рабочей областью Машинное обучение Azure — это каталоги (эксперименты), которые содержат исходный код (обучающие скрипты). Эти сценарии хранятся на локальном компьютере и в облаке (в хранилище BLOB-объектов Azure для вашей подписки). Моментальные снимки кода используются для выполнения или проверки исторического аудита.

[Рабочий процесс создания моментального снимка кода ![](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Обучение

На следующей схеме показан рабочий процесс обучения.

* Машинное обучение Azure вызывается с ИДЕНТИФИКАТОРом моментального снимка для моментального снимка кода, сохраненного в предыдущем разделе.
* Машинное обучение Azure создает идентификатор запуска (необязательно) и маркер службы Машинное обучение, который позже используется целевыми объектами вычислений, такими как Вычислительная среда Машинного обучения или виртуальные машины для взаимодействия со службой Машинное обучение.
* Для выполнения заданий обучения можно выбрать управляемый целевой объект вычислений (например, Вычислительная среда Машинного обучения) или неуправляемый целевой объект вычислений (например, виртуальные машины). Ниже приведены потоки данных для обоих сценариев.
   * Виртуальные машины/HDInsight, доступ к которым осуществляется с помощью учетных данных SSH в хранилище ключей в подписке Майкрософт. Машинное обучение Azure выполняет код управления на целевом объекте вычислений, который:

   1. Подготавливает среду. (DOCKER является вариантом для виртуальных машин и локальных компьютеров. Чтобы понять, как работают эксперименты в контейнерах DOCKER, ознакомьтесь со следующими шагами: Вычислительная среда Машинного обучения.)
   1. Скачивает код.
   1. Настраивает переменные и конфигурации среды.
   1. Запускает пользовательские сценарии (моментальный снимок кода, упомянутый в предыдущем разделе).

   * Вычислительная среда Машинного обучения, доступ к которым осуществляется с помощью удостоверения, управляемого рабочей областью.
Поскольку Вычислительная среда Машинного обучения является управляемым целевым объектом вычислений (т. е. управляется корпорацией Майкрософт), он выполняется в вашей подписке Майкрософт.

   1. При необходимости начинается Удаленная конструкция DOCKER.
   1. Код управления записывается в общую папку пользователя в службе файлов Azure.
   1. Контейнер запускается с помощью начальной команды. Это код управления, как описано в предыдущем шаге.

#### <a name="querying-runs-and-metrics"></a>Запросы на запуски и метрики

На приведенной ниже схеме потока этот шаг выполняется, когда целевой объект для обучения записывает метрики выполнения обратно в Машинное обучение Azure из хранилища в базе данных Cosmos DB. Клиенты могут вызывать Машинное обучение Azure. Машинное обучение будет включать метрики извлечения из базы данных Cosmos DB и возвращать их обратно клиенту.

[Рабочий процесс обучения ![](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Создание веб-служб

На следующей диаграмме показан рабочий процесс определения. Вывод или оценка модели — это этап, в котором развернутая модель используется для прогнозирования, чаще всего в рабочих данных.

Дополнительные сведения:

* Пользователь регистрирует модель с помощью клиента, например пакета SDK для Машинное обучение Azure.
* Пользователь создает образ с помощью модели, файла оценки и других зависимостей модели.
* Образ DOCKER создается и сохраняется в реестре контейнеров Azure.
* Веб-служба развертывается на целевом объекте вычислений (instance Containers/AKS) с помощью образа, созданного на предыдущем шаге.
* Сведения о запросе оценки хранятся в Application Insights, который находится в подписке пользователя.
* Данные телеметрии также отправляются в подписку Microsoft/Azure.

[Рабочий процесс определения ![](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование Машинное обучениеной модели, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Run batch predictions on large data sets with Azure Machine Learning service](how-to-use-parallel-run-step.md) (Составление пакетных прогнозов для больших наборов данных с помощью Службы машинного обучения Azure)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
* [Пакет SDK для Машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Использование Машинное обучение Azure с виртуальной сетью Azure](how-to-enable-virtual-network.md)
* [Recommenders](https://github.com/Microsoft/Recommenders) (Системы рекомендаций)
* [Создание API рекомендаций в режиме реального времени в Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
