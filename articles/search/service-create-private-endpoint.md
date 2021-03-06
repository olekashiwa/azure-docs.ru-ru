---
title: Создание частной конечной точки для безопасного подключения
titleSuffix: Azure Cognitive Search
description: Настройка частной конечной точки в виртуальной сети для безопасного подключения к службе Когнитивный поиск Azure
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945823"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Создание частной конечной точки для безопасного подключения к Azure Когнитивный поиск (Предварительная версия)

В этой статье с помощью портала создается новый экземпляр службы Когнитивный поиск Azure, доступ к которому через общедоступный IP-адрес невозможен. Затем настройте виртуальную машину Azure в той же виртуальной сети и используйте ее для доступа к службе поиска через частную конечную точку.

> [!Important]
> Поддержка частных конечных точек для Azure Когнитивный поиск доступна по [запросу](https://aka.ms/SearchPrivateLinkRequestAccess) в виде предварительной версии с ограниченным доступом. Функции предварительной версии предоставляются без соглашения об уровне обслуживания и не рекомендуются для рабочих нагрузок. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> После предоставления доступа к предварительной версии вы сможете настроить частные конечные точки для службы с помощью портал Azure или [управления REST API версии 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Зачем использовать частную конечную точку для безопасного доступа?

[Частные конечные точки](../private-link/private-endpoint-overview.md) для Azure когнитивный Поиск позволяют клиенту в виртуальной сети безопасно получать доступ к данным в индексе поиска по [закрытой ссылке](../private-link/private-link-overview.md). Частная конечная точка использует IP-адрес из [адресного пространства виртуальной сети](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) для службы поиска. Сетевой трафик между клиентом и службой поиска проходит по виртуальной сети и частной связи в магистральной сети Майкрософт, что устраняет уязвимость в общедоступном Интернете. Список других служб PaaS, поддерживающих частную связь, см. в [разделе Доступность](../private-link/private-link-overview.md#availability) документации по продукту.

Частные конечные точки для службы поиска позволяют:

- Блокировать все подключения в общедоступной конечной точке для службы поиска.
- Увеличьте безопасность виртуальной сети, позволяя блокировать утечка данных из виртуальной сети.
- Безопасное подключение к службе поиска из локальных сетей, которые подключаются к виртуальной сети с помощью [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) или [которыми](../expressroute/expressroute-locations.md) с частными пиринга.

> [!NOTE]
> В настоящее время в предварительной версии есть некоторые ограничения, о которых следует знать:
> * Доступно только для служб поиска на уровне **Basic** . 
> * Доступен в регионах "Западная часть США 2", "Западная Центральная часть США", Восточная часть US, Юго-Центральный регион США, Восточная Австралия и Юго-Восточная Австралия.
> * Если конечная точка службы закрыта, некоторые функции портала отключены. Вы сможете просматривать сведения об уровне обслуживания и управлять ими, но доступ на портал к данным индекса и различные компоненты службы, такие как индексы, индексаторы и определения навыков, ограничены по соображениям безопасности.
> * Если конечная точка службы является частной, необходимо использовать [REST API поиска](https://docs.microsoft.com/rest/api/searchservice/) для отправки документов в индекс.
> * Чтобы увидеть параметр поддержки закрытых конечных точек в портал Azure, необходимо использовать следующую ссылку: https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Запрос доступа 

Щелкните [запросить доступ](https://aka.ms/SearchPrivateLinkRequestAccess) , чтобы зарегистрироваться для получения этой предварительной версии функции. Форма запрашивает сведения о вас, вашей компании и общей топологии сети. После просмотра запроса вы получите сообщение электронной почты с подтверждением с дополнительными инструкциями.

## <a name="create-the-virtual-network"></a>Создание виртуальной сети

В этом разделе вы создадите виртуальную сеть и подсеть для размещения виртуальной машины, которая будет использоваться для доступа к частной конечной точке службы поиска.

1. На вкладке портал Azure Главная выберите **создать ресурс** > **сети** > **Виртуальная сеть**.

1. В подменю **Создать виртуальную сеть** введите или выберите следующую информацию:

    | Параметр | Значение |
    | ------- | ----- |
    | Имя | Введите *MyVirtualNetwork* |
    | Пространство адресов | Введите *10.1.0.0/16* |
    | Subscription | Выберите свою подписку.|
    | Группа ресурсов | Выберите **создать**, введите *myResourceGroup*, а затем нажмите кнопку **ОК** . |
    | Расположение | Выберите **Западная часть США** или любой регион, который вы используете.|
    | Имя подсети | Введите *mySubnet* |
    | Диапазон адреса подсети | Введите *10.1.0.0/24* |
    |||

1. Оставьте без изменений значения остальных параметров и выберите **Создать**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Создание службы поиска с закрытой конечной точкой

В этом разделе вы создадите новую службу Когнитивный поиск Azure с частной конечной точкой. 

1. В верхней левой части экрана портал Azure выберите **создать ресурс** > **веб-**  > **Azure когнитивный Поиск**.

1. В статье **новые служба поиска — основы**введите или выберите следующие сведения:

    | Параметр | Значение |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | Subscription | Выберите свою подписку. |
    | Группа ресурсов | Выберите **myResourceGroup**. Вы создали ее в предыдущем разделе.|
    | **Подробности об экземпляре** |  |
    | URL-адрес | Укажите уникальное имя. |
    | Расположение | Выберите регион, указанный при запросе доступа к этой предварительной версии функции. |
    | Уровень цен | Щелкните **изменить ценовую категорию** и выберите **базовый**. Этот уровень является обязательным для предварительной версии. |
    |||
  
1. Выберите **Далее: масштаб**.

1. Оставьте значения по умолчанию и нажмите кнопку **Далее: сеть**.

1. В **новом служба поиска — сеть**выберите **частный** для **подключения к конечной точке (данные)** .

1. В разделе **новая служба поиска — сеть**выберите **+ Добавить** в области **Частная конечная точка**. 

1. В окне **Создание частной конечной точки**введите или выберите следующие сведения:

    | Параметр | Значение |
    | ------- | ----- |
    | Subscription | Выберите свою подписку. |
    | Группа ресурсов | Выберите **myResourceGroup**. Вы создали ее в предыдущем разделе.|
    | Расположение | Выберите **Западная часть США**.|
    | Имя | Введите *миприватиндпоинт*.  |
    | Целевой подресурс | Оставьте **сеарчсервице**по умолчанию. |
    | **СЕТИ** |  |
    | Виртуальная сеть  | Выберите *MyVirtualNetwork* из группы ресурсов *myResourceGroup*. |
    | Подсеть | Выберите *mySubnet*. |
    | **ЧАСТНАЯ ИНТЕГРАЦИЯ DNS** |  |
    | Интеграция с частной зоной DNS  | Оставьте значение по умолчанию **Да**. |
    | Частная зона DNS  | Оставьте значение по умолчанию * * (новое) privatelink.search.windows.net * *. |
    |||

1. Нажмите кнопку **ОК**. 

1. Выберите **Review + create** (Просмотреть и создать). Вы будете перенаправлены на страницу **Просмотр и создание**, где Azure проверит вашу конфигурацию. 

1. При появлении сообщения **Проверка пройдена** нажмите кнопку **Создать**. 

1. После завершения подготовки новой службы перейдите к только что созданному ресурсу.

1. Выберите **ключи** в меню содержимого слева.

1. Скопируйте **первичный ключ администратора** для последующего использования при подключении к службе.

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

1. В верхней левой части экрана портал Azure выберите **создать ресурс** > **вычислений** > **Виртуальная машина**.

1. В окне **Создание виртуальной машины — Основы** введите или выберите следующую информацию:

    | Параметр | Значение |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | Subscription | Выберите свою подписку. |
    | Группа ресурсов | Выберите **myResourceGroup**. Вы создали ее в предыдущем разделе.  |
    | **Подробности об экземпляре** |  |
    | Имя виртуальной машины | Введите *myVm*. |
    | Регион | Выберите **Западная часть США** или любой регион, который вы используете. |
    | Параметры доступности | Оставьте значение по умолчанию **No infrastructure redundancy required** (Избыточность инфраструктуры не требуется). |
    | Изображение | Выберите **Центр обработки данных Windows Server 2019**. |
    | Размер | Оставьте значение по умолчанию **Standard DS1 v2**. |
    | **Учетная запись администратора** |  |
    | Имя пользователя | Введите выбранное имя пользователя. |
    | Пароль | Введите выбранный пароль. Пароль должен включать минимум 12 символов и соответствовать [определенным требованиям к сложности](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Подтверждение пароля | Введите пароль еще раз. |
    | **Правила входящего порта** |  |
    | Общедоступные входящие порты | Оставьте значение по умолчанию **Разрешить выбранные порты**. |
    | Выбрать входящие порты | Оставьте протокол RDP по умолчанию **(3389)** . |
    | **Экономия** |  |
    | Already have a Windows license? (У вас уже есть лицензия Windows?) | Оставьте значение по умолчанию **Нет**. |
    |||

1. Выберите **Далее: диски**.

1. В окне **Создание виртуальной машины — диски**оставьте значения по умолчанию и нажмите кнопку **Далее: Сетевые подключения**.

1. В окне **Создание виртуальной машины — Сети** выберите такую информацию:

    | Параметр | Значение |
    | ------- | ----- |
    | Виртуальная сеть | Оставьте значение по умолчанию **myVirtualNetwork**.  |
    | Пространство адресов | Оставьте значение по умолчанию **10.1.0.0/24**.|
    | Подсеть | Оставьте значение по умолчанию **mySubnet (10.1.0.0/24)** .|
    | Общедоступный IP-адрес | Оставьте значение по умолчанию **(new) myVm-ip**. |
    | Общедоступные входящие порты | Выберите **Разрешить выбранные порты**. |
    | Выбрать входящие порты | Выберите **HTTP** и **RDP**.|
    ||

1. Выберите **Review + create** (Просмотреть и создать). Вы будете перенаправлены на страницу **Просмотр и создание**, где Azure проверит вашу конфигурацию.

1. При появлении сообщения **Проверка пройдена** нажмите кнопку **Создать**. 


## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Скачайте и подключитесь к виртуальной машине *myVm* следующим образом:

1. На портале в строке поиска введите *myVm*.

1. Нажмите кнопку **Подключиться**. После нажатия кнопки **Подключиться** откроется окно **Connect to virtual machine** (Подключение к виртуальной машине).

1. Щелкните **Скачать RDP-файл**. Azure создаст и скачает на ваш компьютер файл протокола удаленного рабочего стола (*RDP*).

1. Откройте скачанный RDP-файл*.

    1. При появлении запроса выберите **Подключиться**.

    1. Введите имя пользователя и пароль, указанные при создании виртуальной машины.

        > [!NOTE]
        > Возможно, потребуется выбрать **More choices** > **Use a different account** (Дополнительные варианты > Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины.

1. Нажмите кнопку **ОК**.

1. При входе в систему может появиться предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

1. Когда появится рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.  


## <a name="test-connections"></a>Проверка подключения

В этом разделе вы проверите доступ частной сети к службе поиска и подключитесь к, используя закрытую конечную точку.

Вспомним, что для всех взаимодействий со службой поиска требуется [REST API поиска](https://docs.microsoft.com/rest/api/searchservice/). В этой предварительной версии не поддерживаются портал и пакет SDK для .NET.

1. На удаленном рабочем столе  *myVm* откройте PowerShell.

1. Введите команду "nslookup [имя службы поиска]. Search. Windows. NET"

    Должно появиться сообщение следующего вида:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. На виртуальной машине подключитесь к службе поиска и создайте индекс. В этом [кратком руководстве](search-get-started-postman.md) вы можете создать новый индекс поиска в службе в POST с помощью REST API. Для настройки запросов от публикации требуется конечная точка службы поиска (HTTPS://[имя службы поиска]. Search. Windows. NET) и ключ API администратора, скопированный на предыдущем шаге.

1. Завершение краткого руководства по виртуальной машине — подтверждение того, что служба полностью работоспособна.

1. Закройте подключение к удаленному рабочему столу *myVM*. 

1. Чтобы убедиться, что служба недоступна в общедоступной конечной точке, откройте POST на локальной рабочей станции и попытайтесь выполнить первые несколько задач в кратком руководстве. Если появится сообщение о том, что удаленный сервер не существует, вы успешно настроили частную конечную точку для службы поиска.

## <a name="clean-up-resources"></a>Очистка ресурсов 
Завершив использование частной конечной точки, службы поиска и виртуальной машины, удалите группу ресурсов и все содержащиеся в ней ресурсы.
1. Введите *myResourceGroup* в поле **Поиск** в верхней части портала и выберите *myResourceGroup* в результатах поиска. 
1. Выберите **Удалить группу ресурсов**. 
1. Введите *myResourceGroup* **в поле введите имя группы ресурсов** и выберите **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы создали виртуальную машину в виртуальной сети и службу поиска с закрытой конечной точкой. Вы подключились к виртуальной машине из Интернета и безопасно взаимодействовали со службой поиска, используя закрытую ссылку. Дополнительные сведения о частных конечных точках см. в статье [что такое частная конечная точка Azure?](../private-link/private-endpoint-overview.md).
