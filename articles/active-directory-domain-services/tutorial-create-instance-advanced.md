---
title: Руководство. Создание экземпляра доменных служб Azure Active Directory | Документация Майкрософт
description: В этом учебнике описано, как создать и настроить экземпляр доменных служб Azure Active Directory и указать расширенные параметры конфигурации с помощью портала Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 5e969ed4f525d0b3d17339b9f9a6111ad81b0125
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931640"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Руководство. созданию и настройке экземпляра доменных служб Azure Active Directory с помощью расширенных параметров конфигурации

Доменные службы Azure Active Directory (Azure AD DS) предоставляют управляемые доменные службы, отвечающие за присоединение к домену, применение групповой политики, использование протокола LDAP, а также выполнение аутентификации Kerberos или NTLM (полностью поддерживается Windows Server Active Directory). Эти доменные службы можно использовать без необходимости развертывать, администрировать и обновлять контроллеры домена. Azure AD DS интегрируется с существующим клиентом Azure AD. Такая интеграция позволяет пользователям входить в систему с корпоративными учетными данными. При этом вы можете использовать существующие группы и учетные записи пользователей для защиты доступа к ресурсам.

Вы можете [создать управляемый домен, используя параметры конфигурации по умолчанию][tutorial-create-instance] для сетевых подключений и синхронизации, или вручную определить эти параметры. В этом учебнике описано, как определить эти расширенные параметры конфигурации, чтобы создать и настроить экземпляр Azure AD DS на портале Azure.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * настройка параметров DNS и виртуальной сети для управляемого домена;
> * Создание экземпляра Azure AD DS
> * Добавление пользователей с правами администратора в службу управления доменами
> * Включение синхронизации хэшированных паролей

Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Привилегии *глобального администратора* для клиента Azure AD, чтобы включить доменные службы Azure AD.
* Для создания нужных ресурсов Azure AD DS требуются привилегии *участника* в подписке Azure.

Хотя это и не обязательно для Azure AD DS, рекомендуем [настроить самостоятельный сброс пароля (SSPR)][configure-sspr] для клиента Azure AD. Пользователи могут менять свой пароль без SSPR. Однако SSPR помогает в случае, если они забыли пароль и им нужно сбросить его.

> [!IMPORTANT]
> После создания управляемого домена AD DS Azure вы не сможете переместить экземпляр в другую группу ресурсов, виртуальную сеть, подписку и т. д. Выберите наиболее подходящую подписку, группу ресурсов, регион и виртуальную сеть при развертывании экземпляра AD DS Azure.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

При работе с этим руководством вы создадите и настроите экземпляр Azure AD DS с помощью портала Azure. Чтобы начать работу, войдите на [портал Azure](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Создание экземпляра и настройка основных параметров

Чтобы запустить мастер **включения доменных служб Azure AD**, выполните указанные ниже действия.

1. На **домашней странице** или в меню портала Azure выберите **Создать ресурс**.
1. В строке поиска введите *Доменные службы* и выберите *Доменные службы Azure AD* в списке вариантов.
1. На странице "Доменные службы Azure AD" щелкните **Создать**. Запустится мастер **включения доменных служб Azure AD**.
1. Выберите **подписку** Azure, в которой следует создать управляемый домен.
1. Выберите **группу ресурсов**, к которой должен относиться управляемый домен. Щелкните **Создать** или выберите существующую группу ресурсов.

При создании экземпляра Azure AD DS указывается имя DNS. При выборе имени DNS следует учитывать ряд рекомендаций.

* **Встроенное доменное имя.** По умолчанию используется стандартное доменное имя каталога (с суффиксом *.onmicrosoft.com*). Если вы хотите разрешить для управляемого домена доступ по защищенному протоколу LDAP, вы не сможете создать цифровой сертификат для защиты подключения к домену по умолчанию. Домен *onmicrosoft.com* принадлежит корпорации Майкрософт, поэтому центр сертификации не будет выдавать для него сертификаты.
* **Имена личных доменов.** Популярный подход заключается в том, чтобы указать имя личного домена. Обычно оно принадлежит вашей организации и поддерживает маршрутизацию. При использовании личного домена с поддержкой маршрутизации трафик можно передавать так, нужно приложениям.
* **Суффиксы доменов, не поддерживающие маршрутизацию.** Обычно мы рекомендуем не использовать суффиксы доменных имен, не поддерживающие маршрутизацию, такие как *contoso.local*. Суффикс *.local* не маршрутизируется и его использование может привести к ошибкам при разрешении имен DNS.

> [!TIP]
> Если вы создаете личное доменное имя, важно не допускать конфликтов с существующими пространствами имен DNS. Мы рекомендуем включать в доменное имя уникальный префикс. Например, если вы используете корневое имя DNS *contoso.com*, создайте управляемый домен Azure AD DS с личным доменным именем *corp.contoso.com* или *ds.contoso.com*. В гибридной среде с локальной средой AD DS эти префиксы могут уже использоваться. Используйте для Azure AD DS уникальный префикс.
>
> Вы можете использовать для управляемого домена Azure AD DS корневое имя DNS, но это может потребовать дополнительных записей DNS для других служб в той же среде. Например, если корневое имя DNS используется в качестве адреса сайта, размещенного на веб-сервере, могут возникнуть конфликты имен, для устранения которых потребуются дополнительные записи DNS.
>
> В этих учебниках и инструкциях в качестве примера используется личный домен *aadds.contoso.com*. Используйте во всех командах собственное доменное имя, которое может включать уникальный префикс.
>
> Дополнительные сведения см. в инструкции по [выбору префикса именования для домена].

Также применимы следующие ограничения в отношении DNS:

* **Ограничения для префикса домена.** Невозможно создать управляемый домен с префиксом длиннее 15 символов. Длина указанного префикса доменного имени (например, *contoso* в доменном имени *contoso.com*) не должна превышать 15 символов.
* **Конфликты сетевых имен.** Нельзя использовать для управляемого домена имя DNS, которое уже существует в виртуальной сети. В частности, к конфликту имен приведут следующие сценарии:
    * Если в виртуальной сети уже существует домен Active Directory с таким же доменным именем DNS.
    * Если для виртуальной сети, в которой планируется использовать управляемый домен, установлено VPN-подключение к локальной сети. в этом случае необходимо убедиться в том, что в локальной сети нет домена с таким же DNS-именем домена;
    * Если в виртуальной сети Azure существует облачная служба Azure с таким же именем.

Заполните поля в окне *Основные данные* на портале Azure, чтобы создать экземпляр Azure AD DS.

1. Введите **доменное имя DNS** для управляемого домена, учитывая описанные выше ограничения.
1. Выберите **расположение** Azure, в котором необходимо создать управляемый домен. Если вы выбрали регион, который поддерживает зоны доступности, ресурсы Azure AD DS распределяются между зонами для дополнительной избыточности.

    Зоны доступности — уникальные физические расположения в пределах одного региона Azure. Каждая зона состоит из одного или нескольких центров обработки данных, оснащенных независимыми системами электроснабжения, охлаждения и сетевого взаимодействия. Чтобы обеспечить отказоустойчивость, во всех включенных регионах используются минимум три отдельные зоны.

    Вы не можете настроить распределение Azure AD DS между зонами. Платформа Azure автоматически обрабатывает распределение ресурсов зоны. Дополнительные сведения о зонах доступности и регионах см. в статье [Что такое зоны доступности в Azure?][availability-zones].

1. **SKU** определяет производительность, частоту резервного копирования и максимальное число отношений доверия, которые можно создать для лесов. Если коммерческие или другие требования изменятся, SKU можно будет изменить после создания управляемого домена. Дополнительные сведения см. в статье об [основных понятиях SKU Azure AD DS][concepts-sku].

    Для работы с этим руководством выберите SKU категории *Стандартный*.
1. *Лес* — это логическая конструкция, используемая доменными службами Active Directory для группирования одного или нескольких доменов. По умолчанию управляемый домен AD DS Azure создается как лес *пользователей*. Лес этого типа синхронизирует все объекты из Azure AD, включая учетные записи пользователей, созданные в локальной среде AD DS. Лес *ресурсов* синхронизирует только пользователей и группы, созданные непосредственно в Azure AD. Леса ресурсов в настоящее время доступны в предварительной версии. Дополнительные сведения о лесах *ресурсов*, в том числе об их предназначении и о создании доверия между лесами и локальными доменами AD DS, см. в [этой статье][resource-forests].

    Для работы с этим учебником создайте лес *пользователей*.

    ![Настройка базовых параметров для экземпляра доменных служб Azure AD](./media/tutorial-create-instance-advanced/basics-window.png)

1. Чтобы вручную настроить дополнительные параметры, выберите **Далее — Сетевые подключения**. В противном случае выберите **Просмотр и создание**, чтобы принять параметры конфигурации по умолчанию, а затем перейдите к разделу [Развертывание управляемого домена](#deploy-the-managed-domain). При выборе этого параметра создания настраиваются следующие значения по умолчанию:

    * Создается виртуальная сеть с именем *aadds-vnet*, которая использует диапазон IP-адресов *10.0.1.0/24*.
    * Создается подсеть с именем *aadds-vnet*, которая использует диапазон IP-адресов *10.0.1.0/24*.
    * Синхронизируются *все* пользователи из Azure AD в управляемый домен Azure AD DS.

## <a name="create-and-configure-the-virtual-network"></a>Создание и настройка виртуальной сети.

Чтобы настроить подключение, требуются виртуальная сеть Azure и выделенная подсеть. Службы Azure AD DS включены в этой подсети виртуальной сети. В этом руководстве описано, как создать виртуальную сеть, но вместо этого можно использовать уже существующую. В любом случае вам нужно создать выделенную подсеть для Azure AD DS.

При использовании выделенной виртуальной сети необходимо учитывать следующее:

* В подсети должны быть доступными не менее 3–5 свободных IP-адресов для ресурсов Azure AD DS.
* Не выбирайте подсеть *Gateway* для развертывания Azure AD DS. Развертывание Azure AD DS в *подсети шлюза* не поддерживается.
* Не развертывайте другие виртуальные машины в этой подсети. Приложения и виртуальные машины часто используют группы безопасности сети для защиты подключения. Выполнение рабочих нагрузок в отдельной подсети позволяет применять группы безопасности сети, не влияя на подключение к управляемому домену.
* После включения Azure AD DS переместить управляемый домен в другую виртуальную сеть нельзя.

См. сведения о проектировании и настройке виртуальной сети в [рекомендациях по использованию сетей для доменных служб Azure AD][network-considerations].

Заполните поля в окне *Сеть*, как описано ниже.

1. На странице **Сеть** выберите виртуальную сеть, в которую будет развертываться Azure AD DS, в раскрывающемся меню или выберите **Создать**.
    1. При создании виртуальной сети введите ее имя, например *myVnet*, и укажите диапазон адресов, например *10.0.1.0/24*.
    1. Создайте выделенную подсеть с понятным именем, например *DomainServices*. Укажите диапазон адресов, например *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Выбранное адресное пространство должно входить в пространство частных IP-адресов. Использование не принадлежащих вам IP-адресов из пространства общедоступных адресов приведет к сбоям в работе доменных служб Azure AD.

1. Выберите подсеть виртуальной сети, например *DomainServices*.
1. Когда все будет готово, выберите **Далее — Администрирование**.

## <a name="configure-an-administrative-group"></a>Настройка административной группы

Для управления доменом Azure AD DS используется специальная административная группа с именем *Администраторы AAD DC*. Участникам этой группы предоставляются разрешения администратора на виртуальных машинах, присоединенных к управляемому домену. На виртуальных машинах, присоединенных к домену, эта группа добавляется в группу локальных администраторов. Участники этой группы могут подключаться по протоколу удаленного рабочего стола к виртуальным машинам, присоединенным к домену.

При работе с Azure AD DS вы не получаете разрешения *администратора домена* или *администратора предприятия* для управляемого домена. Эти разрешения зарезервированы службой и не предоставляются для пользователей в клиенте. Вместо этого для некоторых операций с повышенным уровнем доступа можно применять группу *Администраторы AAD DC*. Сюда относятся присоединение компьютеров к домену, добавление в группу администраторов на виртуальных машинах, присоединенных к домену, и настройка групповой политики.

Мастер автоматически создает группу *Администраторы AAD DC* в каталоге Azure AD. Если у вас уже есть группа с таким именем в каталоге Azure AD, мастер выберет ее. При желании вы можете добавить других пользователей в группу *Администраторы AAD DC* в ходе развертывания. Эти действия можно выполнить и позже.

1. Чтобы добавить пользователей в группу *Администраторы AAD DC* щелкните **Управление членством в группах**.

    ![Настройка членства в группе "Администраторы AAD DC"](./media/tutorial-create-instance-advanced/admin-group.png)

1. Щелкните **Добавить участников**, а затем найдите и добавьте пользователей из каталога Azure AD. Например, найдите и добавьте собственную учетную запись в группу *Администраторы AAD DC*.
1. При необходимости измените или добавьте дополнительных получателей уведомлений при наличии оповещений в управляемом домене Azure AD DS, требующих внимания.
1. Когда все будет готово, выберите **Далее — Синхронизация**.

## <a name="configure-synchronization"></a>Настройка синхронизации

Службы Azure AD DS позволяют синхронизировать *всех пользователей и все группы*, которые доступны в Azure AD, или только *определенные группы*. Если вы решите синхронизировать *всех пользователей и все группы*, вы не сможете позже перейти на ограниченную синхронизацию. См. сведения об [ограниченной синхронизации в области в доменных службах Azure AD][scoped-sync].

1. Для нашего примера выберите синхронизацию **всех пользователей и групп**. Этот вариант синхронизации используется по умолчанию.

    ![Выполнение полной синхронизации пользователей и групп из Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Выберите **Review + create** (Просмотреть и создать).

## <a name="deploy-the-managed-domain"></a>Развертывание управляемого домена

На странице **Сводка** просмотрите параметры конфигурации для управляемого домена. Вы всегда можете вернуться к любому шагу мастера и внести изменения. Чтобы согласованно повторно развернуть управляемый домен Azure AD DS в другой клиент Azure AD с помощью этих же параметров конфигурации, можно также выполнить действие **Загрузить шаблон для автоматизации**.

1. Чтобы создать управляемый домен, щелкните **Создать**. Отобразится примечание о том, что некоторые параметры конфигурации, такие как DNS-имя или виртуальная сеть, нельзя изменить после создания управляемого домена Azure AD DS. Чтобы продолжить, нажмите кнопку **ОК**.
1. Процесс подготовки управляемого домена может занять до одного часа. На портале отображается уведомление, которое демонстрирует ход выполнения развертывания Azure AD DS. Щелкните это уведомление, чтобы просмотреть подробное описание процесса развертывания.

    ![Уведомление на портале Azure для выполняемого развертывания](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Выберите группу ресурсов, например *myResourceGroup*, а затем выберите в списке ресурсов Azure экземпляр Azure AD DS, например *aadds.contoso.com*. На вкладке **Обзор** можно увидеть, что сейчас выполняется *развертывание* управляемого домена. Настроить управляемый домен можно только после его полной подготовки.

    ![Состояние доменных служб на этапе подготовки](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. После полной подготовки управляемого домена на вкладке **Обзор** состояние домена отображается как *Выполняется*.

    ![Состояние доменных служб после успешного завершения подготовки](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

Управляемый домен связан с вашим клиентом Azure AD. В процессе подготовки Azure AD DS создает в вашем клиенте Azure AD два корпоративных приложения с именами *Domain Controller Services* и *AzureActiveDirectoryDomainControllerServices*. Эти корпоративные приложения нужны для обслуживания управляемого домена. Не удаляйте эти приложения.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Обновление настроек DNS для виртуальной сети Azure

После успешного развертывания Azure AD DS следует настроить виртуальную сеть, чтобы другие подключенные виртуальные машины и приложения могли использовать управляемый домен. Чтобы создать такое подключение, измените параметры DNS-сервера для виртуальной сети, указав IP-адреса развертывания Azure AD DS.

1. На вкладке **Обзор** для управляемого домена отображаются некоторые **требуемые шаги конфигурации**. Первый из них — обновление параметров DNS-сервера для виртуальной сети. После правильной настройки параметров DNS этот шаг исчезает из списка.

    Указанные здесь адреса принадлежат контроллерам домена для использования в виртуальной сети. В нашем примере это адреса *10.1.0.4* и *10.1.0.5*. Эти IP-адреса вы можете найти на вкладке **Свойства**.

    ![Включение IP-адресов доменных служб Azure AD в параметры DNS для виртуальной сети](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Щелкните **Настроить**, чтобы обновить параметры DNS-сервера для виртуальной сети. Параметры DNS автоматически настраиваются для виртуальной сети.

> [!TIP]
> Если на предыдущих шагах вы выбрали существующую виртуальную сеть, все виртуальные машины в этой сети получат новые параметры DNS только после перезапуска. Виртуальные машины можно перезапустить с помощью портала Azure, Azure CLI или Azure PowerShell.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Включение учетных записей пользователей для Azure AD DS

Чтобы выполнять аутентификацию пользователей в управляемом домене, Azure AD DS использует хэши паролей в формате, требуемом для аутентификации NTLM и Kerberos. Пока вы не включите Azure AD DS для клиента, Azure AD не будет создавать и хранить хэши паролей в формате, требуемом для аутентификации NTLM или Kerberos. Из соображений безопасности Azure AD никогда не хранит пароли в виде открытого текста. Поэтому Azure AD не может автоматически создать хэши паролей в формате NTLM или Kerberos по уже существующим учетным данным пользователей.

> [!NOTE]
> После настройки хэши паролей в требуемом формате сохраняются в управляемом домене Azure AD DS. Если вы удалите управляемый домен Azure AD DS, вместе с ним будут удалены все сохраненные хэши паролей. Синхронизированные в Azure AD учетные данные нельзя использовать повторно, если вы снова создадите управляемый домен AD DS Azure. Вам придется повторно настраивать синхронизацию хэшей паролей, чтобы сохранить их. Ранее присоединенные к домену виртуальные машины и пользователи не смогут сразу выполнить аутентификацию, пока Azure AD не создаст и не сохранит хэши паролей в новом управляемом домене Azure AD DS. См. сведения о [синхронизации хэшей паролей в Azure AD DS и Azure AD Connect][password-hash-sync-process].

Этапы создания и сохранения хэшей паролей будут разными для облачных учетных записей, созданных в Azure AD, и учетных записей, синхронизируемых из локального каталога с помощью Azure AD Connect. Облачная учетная запись пользователей — это учетная запись, созданная в каталоге Azure AD с помощью портала Azure или командлетов Azure AD PowerShell. Такие учетные записи пользователей не синхронизируются из локального каталога. В этом руководстве мы будем использовать простой вариант облачной учетной записи. Дополнительные шаги, которые потребуются для использования Azure AD Connect, см. в руководстве по [синхронизации хэшей паролей для учетных записей пользователей, синхронизируемых из локального каталога Azure AD в управляемый домен][on-prem-sync].

> [!TIP]
> Если клиент Azure AD содержит и пользователей в облаке, и пользователей из локального каталога Azure AD, необходимо выполнить оба блока действий.

При использовании облачных учетных записей пользователям нужно сменить пароль, чтобы получить доступ к Azure AD DS. При смене пароля хэши паролей автоматически создаются и сохраняются в Azure AD для аутентификации Kerberos и NTLM. Вы можете принудительно завершить срок действия для всех паролей пользователей в клиенте Azure AD DS или сообщить пользователям о необходимости самостоятельно изменить пароли. В этом руководстве описано, как изменить пароль пользователя вручную.

Чтобы пользователь мог сменить свой пароль, для клиента Azure AD должна быть включена [функция самостоятельного сброса пароля][configure-sspr].

Чтобы изменить пароль для облачного пользователя, выполните от его имени следующие действия:

1. Откройте Панель доступа Azure AD по адресу [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Вверху справа щелкните имя и выберите **Профиль** в раскрывающемся меню.

    ![Выбор параметра "Профиль"](./media/tutorial-create-instance-advanced/select-profile.png)

1. На странице **Профиль** щелкните **Изменить пароль**.
1. На странице **Изменение пароля** введите существующий (старый) пароль, а затем введите и подтвердите новый пароль.
1. Нажмите кнопку **Submit** (Отправить).

Новый пароль становится работоспособным в Azure AD DS через несколько минут после изменения, позволяя выполнять вход на компьютеры, которые объединены в управляемый домен.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка параметров DNS и виртуальной сети для управляемого домена;
> * Создание экземпляра Azure AD DS
> * Добавление пользователей с правами администратора в службу управления доменами
> * Включение учетных записей пользователей для Azure AD DS и создание хэшей паролей

Чтобы увидеть, как работает управляемый домен, создайте виртуальную машину и присоедините ее к домену.

> [!div class="nextstepaction"]
> [Присоединение виртуальной машины Windows Server к управляемому домену](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
