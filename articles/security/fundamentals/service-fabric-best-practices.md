---
title: Рекомендации по обеспечению безопасности Azure Service Fabric
description: В этой статье представлены рекомендации по безопасности Azure Service Fabric.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 458a1d474e9a722a98ca068e1827cf0e1abf4b47
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75548825"
---
# <a name="azure-service-fabric-security-best-practices"></a>Рекомендации по безопасности Azure Service Fabric
Развертывание приложения в Azure — быстрое, удобное и экономичное. Прежде чем развертывать облачное приложение в рабочей среде, ознакомьтесь с нашим списком важных рекомендаций по реализации защищенных кластеров в приложении.

Azure Service Fabric — это платформа распределенных систем, которая дает возможность не только легко упаковывать и развертывать масштабируемые и надежные микрослужбы, но и управлять ими. Service Fabric также позволяет разрешить значительные трудности, возникающие при разработке облачных приложений и управлении ими. Разработчики и администраторы могут не тратить силы на решение сложных проблем с инфраструктурой, а сосредоточиться на реализации критически важных и ресурсоемких рабочих нагрузок, которые являются масштабируемыми, надежными и управляемыми.

Для каждой рекомендации будет предоставлено объяснение следующих аспектов:

-   в чем заключается рекомендация;
-   почему нужно следовать этой рекомендации;
-   что может произойти, если не выполнить эту рекомендацию;
-   как научиться следовать этой рекомендации.

Сейчас предлагаются следующие рекомендации по обеспечению безопасности в Azure Service Fabric.

-   Используйте шаблоны Azure Resource Manager и модуль PowerShell для Service Fabric для создания защищенных кластеров.
-   Используйте сертификаты X.509.
-   настройке политик безопасности;
-   Реализуйте конфигурацию безопасности на основе Reliable Actors.
-   Настройте SSL для Azure Service Fabric.
-   Обеспечьте изоляцию и безопасность сетей с помощью Azure Service Fabric.
-   Настройте интеграцию с Azure Key Vault для повышения безопасности.
-   Назначайте пользователей для ролей.


## <a name="best-practices-for-securing-your-clusters"></a>Рекомендации по обеспечению безопасности кластеров

Всегда используйте защищенный кластер:
-   реализуйте защиту кластера с помощью сертификатов;
-   предоставьте клиентам доступ (для администрирования и только для чтения) с помощью Azure Active Directory (Azure AD).

Используйте автоматическое развертывание:
-   Используйте сценарии для создания, развертывания и смены секретов.
-   Храните секреты в Azure Key Vault и используйте Azure AD для доступа всех прочих клиентов.
-   Требуйте прохождения аутентификации для доступа пользователей к секретам.

Кроме того, учтите следующие особенности конфигурации:
-   Создайте сети периметра (также называются демилитаризованными зонами и промежуточными подсетями) с помощью групп безопасности сети Azure (NSG).
-   Обращайтесь к виртуальным машинам кластера и управляйте кластером с помощью серверов переходов, используя подключение к удаленному рабочему столу.

Кластеры должны быть защищены, чтобы неавторизованные пользователи не могли подключиться к ним, особенно если кластер выполняется в рабочей среде. Конечно, можно создать незащищенный кластер, что позволит анонимным пользователям подключаться к нему (если конечные точки управления им общедоступны через Интернет).

Существуют три [сценария](../../service-fabric/service-fabric-cluster-security.md) для реализации безопасности кластера с помощью разных технологий.

-   Безопасность обмена данными между узлами. В этом сценарии обеспечивается защита взаимодействия виртуальных машин и компьютеров в кластере. Такая разновидность защиты гарантирует, что размещать приложения и службы в кластере смогут только пользователи тех компьютеров, которые прошли авторизацию для подключения к кластеру.
В этом сценарии кластеры, работающие в Azure, или автономные кластеры, работающие под управлением Windows, могут использовать [безопасность на основе сертификатов](../../service-fabric/service-fabric-windows-cluster-x509-security.md) или [систему безопасности Windows](../../service-fabric/service-fabric-windows-cluster-windows-security.md) для компьютеров Windows Server.
-   Безопасность обмена данными между клиентами и узлами. В этом сценарии обеспечивается защита взаимодействия клиента Service Fabric и отдельных узлов в кластере.
-   Управление доступом на основе ролей (RBAC). В этом сценарии используются отдельные удостоверения (сертификаты, удостоверения Azure AD и т. д) для каждой клиентской роли администратора и пользователя, обращающегося к кластеру. Удостоверения роли указываются при создании кластера.

>[!NOTE]
>**Рекомендация по защите кластеров Azure**. Чтобы обеспечить безопасность обмена данными между узлами, используйте средства безопасности Azure AD для аутентификации клиентов и проверки подлинности сертификатов.

Сведения о настройке автономного кластера Windows см. в статье [Параметры конфигурации для автономного кластера Windows](../../service-fabric/service-fabric-cluster-manifest.md).

Используйте шаблоны Azure Resource Manager и модуль PowerShell для Service Fabric для создания защищенного кластера.
Пошаговые инструкции по созданию кластера Service Fabric с помощью шаблонов Azure Resource Manager см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Используйте шаблон Azure Resource Manager:
-   Настройте кластер с помощью шаблона, чтобы задать параметры управляемого хранилища для виртуальных жестких дисков виртуальных машин.
-   Вносите изменения в группу ресурсов с помощью шаблона, чтобы упростить управление конфигурацией и ее аудит.

Рассматривайте конфигурацию кластера как код:
-   Тщательно проверяйте конфигурацию своих развертываний.
-   Избегайте использования неявных команд для непосредственного изменения ресурсов.

Многие аспекты [жизненного цикла приложения Service Fabric](../../service-fabric/service-fabric-application-lifecycle.md) можно автоматизировать. [Модуль PowerShell для Service Fabric](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) автоматизирует типовые задачи по развертыванию, обновлению, удалению и тестированию приложений Azure Service Fabric. Также доступны управляемые интерфейсы API и API HTTP для управления приложениями.

## <a name="use-x509-certificates"></a>Использование сертификата X.509.
Кластеры всегда должны быть защищены с помощью сертификатов X.509 или системы безопасности Windows. Параметры безопасности можно настроить только во время создания кластера. Включить защиту после создания кластера невозможно.

Чтобы указать [сертификат кластера](../../service-fabric/service-fabric-windows-cluster-x509-security.md), задайте для свойства **ClusterCredentialType** значение X509. Чтобы указать сертификат сервера для внешних подключений, задайте для свойства **ServerCredentialType** значение X509.

Кроме того, следуйте приведенным ниже рекомендациям.
-   Создавайте сертификаты для рабочих кластеров с помощью правильно настроенной службы сертификации Windows Server. Сертификаты также можно получать из утвержденного центра сертификации (ЦС).
-   Не используйте временный или тестовый сертификат для рабочих кластеров, если этот сертификат был создан с помощью MakeCert.exe или аналогичного инструмента.
-   Используйте самозаверяющий сертификат для тестовых кластеров, но не для рабочих кластеров.

Если кластер не защищен, то к нему в любое время может анонимно подключиться любой пользователь и выполнять операции управления. Поэтому рабочие кластеры всегда должны быть защищены с помощью сертификатов X.509 или системы безопасности Windows.

Дополнительные сведения об использовании сертификатов X.509 см. в статье [Добавление и удаление сертификатов для кластера Service Fabric в Azure](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Настройка политик безопасности.
Service Fabric также защищает ресурсы, которые используются приложениями. Ресурсы, такие как файлы, каталоги и сертификаты, сохраняются в учетных записях пользователей при развертывании приложения. Это позволяет дополнительно защитить друг от друга выполняемые приложения, даже если они запущены в общей размещенной среде.

-   Используйте группу или пользователя домена Active Directory. Запустите службу с использованием учетных данных для учетной записи пользователя или группы Active Directory. Обязательно используйте локальную службу Active Directory в домене, а не Azure Active Directory. Обращайтесь к другим ресурсам в домене, для которых предоставлены разрешения на доступ, с помощью пользователя или группы домена. Например, это могут быть общедоступные файловые ресурсы.

-   Назначьте политику безопасности доступа для конечных точек HTTP и HTTPS: укажите свойство **SecurityAccessPolicy**, чтобы применить политику **запуска от имени** к службе, когда манифест службы объявляет ресурсы конечной точки с помощью протокола HTTP. Порты, выделенные для конечных точек HTTP, правильно контролируются с помощью списков управления доступом для учетной записи запуска от имени пользователя, в которой выполняется служба. Если политика не настроена, у файла http.sys не будет доступа к службе, а вызовы от клиента будут завершаться ошибками.

Чтобы узнать, как использовать политики безопасности в кластере Service Fabric, ознакомьтесь со статьей [Настройка политик безопасности для приложения](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Реализация конфигурации безопасности на основе Reliable Actors
Субъекты Reliable Actors Service Fabric — это реализация шаблона проектирования субъектов. Как и в случае с любым другим шаблоном проектирования программного обеспечения, решение о выборе того или иного шаблона зависит от того, соответствует ли он задаче программного обеспечения.

Как правило, конструктивный шаблон субъекта используется, чтобы моделировать решения для следующих задач программного обеспечения или сценариев безопасности.
-   Проблемное пространство включает множество (тысячи и больше) небольших независимых и изолированных единиц состояния и логики.
-   Вы собираетесь работать с однопоточными объектами, которые не требуют значительного взаимодействия с внешними компонентами, включая запросы состояния в пределах определенного набора субъектов.
-   Экземпляры субъекта не должны блокировать вызывающие объекты с непредсказуемыми задержками, выполняя операции ввода-вывода.

В Service Fabric субъекты реализуются в исполняющей среде Reliable Actors. Эта платформа основана на шаблоне субъекта и [Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md). Каждая созданная вами служба Reliable Actor представляет собой секционированную надежную службу с отслеживанием состояния.

Аналогично объектам .NET, которые являются экземплярами типа .NET, каждый субъект определяется как экземпляр типа субъекта. Например, для **типа субъекта**, который реализует функции калькулятора, может быть много субъектов данного типа, распределенных по разным узлам кластера. Каждый из распределенных субъектов однозначно характеризуется идентификатором субъекта.

[Конфигурации безопасности репликаторов](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) используются для защиты канала связи, который используется во время репликации. Эта конфигурация не позволяет службам отслеживать трафик репликации друг друга и обеспечивает безопасность высокодоступных данных. По умолчанию пустой раздел конфигурации безопасности означает, что канал репликации не защищен.
Конфигурации репликатора используются для настройки репликатора, отвечающего за надежность состояний поставщика состояний субъекта.

## <a name="configure-ssl-for-azure-service-fabric"></a>Настройка SSL для Azure Service Fabric.
Процесс аутентификации сервера [аутентифицирует](../../service-fabric/service-fabric-cluster-creation-via-arm.md) конечные точки управления кластера в клиенте управления. Затем клиент управления определяет, что он взаимодействует с реальным кластером. Этот сертификат также предоставляет [SSL](../../service-fabric/service-fabric-cluster-creation-via-arm.md) для API управления HTTPS и Service Fabric Explorer по протоколу HTTPS.
Необходимо получить имя личного домена для кластера. При запросе сертификата из центра сертификации имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

Чтобы настроить протокол SSL для приложения, сначала необходимо получить SSL-сертификат, подписанный центром сертификации. ЦС является доверенным сторонним производителем, выдающим сертификаты для обеспечения безопасности SSL. Если у вас еще нет SSL-сертификата, нужно получить его в компании, продающей SSL-сертификаты.

Сертификат должен отвечать следующим требованиям для SSL-сертификатов в Azure:
-   Сертификат должен содержать закрытый ключ.

-   Сертификат должен быть создан для обмена ключами, которые можно экспортировать в файл обмена личной информацией (PFX-файл).

-   Имя субъекта сертификата должно совпадать с доменным именем, которое используется для обращения к облачной службе.

    - Получите имя личного домена для доступа к облачной службе.
    - Запросите в ЦС сертификат с именем субъекта, соответствующим имени личного домена вашей службы. Например, если вы используете имя личного домена __contoso__ **.com**, то именем субъекта сертификата из ЦС должно быть **.contoso.com** или __www__ **.contoso.com**.

    >[!NOTE]
    >Невозможно получить SSL-сертификат из ЦС для домена __cloudapp__ **.net**.

-   Сертификат должен использовать как минимум 2048-разрядное шифрование.

Протокол HTTP является небезопасным и часто подвергаются атакам перехвата информации. Данные, передаваемые по протоколу HTTP, отправляются как обычный текст из веб-браузера на веб-сервер или между другими конечными точками. Злоумышленники могут перехватывать и просматривать конфиденциальные данные, отправленные по протоколу HTTP, такие как сведения о кредитной карте и учетные данные. При отправке или публикации данных с помощью браузера по протоколу HTTPS протокол SSL обеспечивает шифрование конфиденциальной информации и ее защиту от перехвата.

Чтобы узнать больше об использовании SSL-сертификатов, ознакомьтесь с разделом [Настройка SSL для приложения в Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Обеспечение изоляции и безопасности сетей с помощью Azure Service Fabric
Для примера настройте защищенный кластер с 3 узлами с помощью [шаблона Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Для контроля входящего и исходящего сетевого трафика используйте шаблон и группы безопасности сети.

Шаблон содержит по одной группе безопасности сети для каждого масштабируемого набора виртуальных машин, которая используется для управления входящим и исходящим трафиком масштабируемого набора. Настроенные по умолчанию правила разрешают весь трафик, необходимый системным службам и портам приложений, указанным в шаблоне. Просмотрите эти правила и измените их в соответствии со своими потребностями, включая добавление новых правил для приложений.

Дополнительные сведения см. в статье [Схемы сетевых подключений Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Настройка Azure Key Vault для обеспечения безопасности
Сертификаты используются в Service Fabric, чтобы обеспечить функции аутентификации и шифрования для защиты кластера и его приложений.

Service Fabric использует сертификаты X.509 для защиты кластера и обеспечения функций безопасности приложений. Azure Key Vault используется для [управления сертификатами](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) кластеров Service Fabric в Azure. Поставщик ресурсов Azure, который создает кластеры, извлекает сертификаты из хранилища ключей. Затем поставщик устанавливает сертификаты на виртуальные машины при развертывании кластера в Azure.

Сертификат устанавливает связь между [Azure Key Vault](../../key-vault/key-vault-secure-your-key-vault.md), кластером Service Fabric и поставщиком ресурсов, который использует сертификаты. При создании кластера сведения о связи сертификата сохраняются в хранилище ключей.

Существуют два основных действия по настройке хранилища ключей.
1. Создание группы ресурсов специально для хранилища ключей.

    Рекомендуется поместить хранилище ключей в собственную группу ресурсов. Это действие помогает предотвратить потерю ключей и секретов при удалении других групп ресурсов (например, содержащих ресурсы хранилища или вычислительные ресурсы) или группы, содержащей кластер. Группа ресурсов с хранилищем ключей должна находиться в том же регионе, что и кластер, который ее использует.

2. Создание хранилища ключей в новой группе ресурсов.

    Хранилище ключей должно быть добавлено в развертывание. В этом случае поставщик вычислительных ресурсов сможет получить сертификаты из хранилища и установить их на экземплярах виртуальных машин.

Чтобы узнать больше о том, как настроить хранилище ключей, ознакомьтесь со статьей об [ Azure Key Vault](../../key-vault/key-vault-overview.md).

## <a name="assign-users-to-roles"></a>Назначение пользователей ролям
После создания приложений для представления кластера Назначьте пользователям роли, которые поддерживаются Service Fabric: только для чтения и администратора. Эти роли можно назначить с помощью портал Azure.

>[!NOTE]
> Дополнительные сведения об использовании ролей в Service Fabric см. в статье [Контроль доступа на основе ролей для клиентов Service Fabric](../../service-fabric/service-fabric-cluster-security-roles.md).

Платформа Azure Service Fabric поддерживает два типа управления доступом для клиентов, подключенных к [кластеру Service Fabric](../../service-fabric/service-fabric-cluster-creation-via-arm.md): администраторский и пользовательский. Администратор кластера может использовать управление доступом, чтобы ограничить доступ к определенным операциям в кластере для разных групп пользователей. Управление доступом повышает защищенность кластера.

## <a name="next-steps"></a>Дальнейшие действия

- [Контрольный список для обеспечения безопасности Azure Service Fabric](service-fabric-checklist.md)
- Настройка [среды разработки](../../service-fabric/service-fabric-get-started.md) Service Fabric.
- Узнайте о [вариантах поддержки Service Fabric](../../service-fabric/service-fabric-support.md).
