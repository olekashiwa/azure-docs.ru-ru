---
title: Руководство по Защита облачной сети с помощью предварительной версии Диспетчера брандмауэра Azure на портале Azure
description: В этом руководстве вы можете узнать, как защитить облачную сеть с помощью Диспетчера брандмауэра Azure на портале Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: victorh
ms.openlocfilehash: d2ebfd6003c0bc2b47636be1e38f47e554cc6988
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510038"
---
# <a name="tutorial-secure-your-cloud-network-with-azure-firewall-manager-preview-using-the-azure-portal"></a>Руководство по Защита облачной сети с помощью предварительной версии Диспетчера брандмауэра Azure на портале Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

С помощью предварительной версии Диспетчера брандмауэра Azure можно создавать защищенные концентраторы для защиты трафика облачной сети, направляемого на частные IP-адреса, в Azure PaaS и Интернет. Трафик маршрутизируется в брандмауэр автоматически, поэтому создавать пользовательские маршруты не требуется.

![защита облачной сети](media/secure-cloud-network/secure-cloud-network.png)

## <a name="prerequisites"></a>Предварительные требования

> [!IMPORTANT]
> Предварительную версию Диспетчера брандмауэра Azure необходимо включить явным образом с помощью команды `Register-AzProviderFeature` в PowerShell.

В командной строке PowerShell выполните следующие команды:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Регистрация функции занимает до 30 минут. Чтобы проверить состояние регистрации, выполните следующую команду:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="create-a-hub-and-spoke-architecture"></a>Создание звездообразной архитектуры

Сначала создайте периферийную виртуальную сеть, в которой можно разместить серверы.

### <a name="create-a-spoke-vnet-and-subnets"></a>Создание периферийной виртуальной сети и подсетей

1. На домашней странице портала Azure выберите **Создать ресурс**.
2. В разделе **Сеть** выберите **Виртуальная сеть**.
4. В поле **Имя** введите **Spoke-01**.
5. В поле **адресного пространства** введите **10.0.0.0/16**.
6. В качестве **подписки** выберите свою подписку.
7. В поле **Группа ресурсов** щелкните **Создать**, введите имя **FW-Manager** и нажмите кнопку **ОК**.
8. В поле **Расположение** выберите **Восточная часть США**.
9. В разделе **Подсеть** в поле **Имя** введите **Workload-SN**.
10. В поле **Диапазон адресов** введите **10.0.1.0/24**.
11. Оставьте значения по умолчанию для других параметров и выберите **Создать**.

Далее создайте подсеть для сервера переходов.

1. На домашней странице портала Azure выберите **Группы ресурсов** > **FW-Manager**.
2. Выберите виртуальную сеть **Spoke-01**.
3. Выберите **Подсети** >  **+Подсеть**.
4. В поле **Имя** введите **Jump-SN**.
5. В поле **Диапазон адресов** введите **10.0.2.0/24**.
6. Нажмите кнопку **ОК**.

### <a name="create-the-secured-virtual-hub"></a>Создание защищенного виртуального концентратора

Создайте защищенный виртуальный концентратор с помощью Диспетчера брандмауэра.

1. На домашней странице портала Azure выберите **Все службы**.
2. В поле поиска введите запрос **Диспетчер брандмауэра** и выберите элемент **Диспетчер брандмауэра**.
3. На странице **Диспетчер брандмауэра** выберите **Создать защищенный виртуальный концентратор**.
4. На странице **Создание защищенного виртуального концентратора** выберите свою подписку и группу ресурсов **FW-Manager**.
5. В поле **Имя защищенного виртуального концентратора** введите **Hub-01**.
6. В поле **Расположение** выберите **Восточная часть США**.
7. В поле **Диапазон адресов концентратора** введите **10.1.0.0/16**.
8. В поле имени новой виртуальной глобальной сети введите **vwan-01**.
9. Не устанавливайте флажок **Включить VPN-шлюз для использования доверенных партнеров по безопасности**.
10. Выберите **Далее: Брандмауэр Azure**.
11. Примите значение по умолчанию **Включено** для **Брандмауэра Azure**, а затем выберите **Далее: Доверенный партнер по безопасности**.
12. Примите значение по умолчанию **Отключено** для параметра **Доверенный партнер по безопасности**, а затем выберите **Далее: Отзыв и создание**.
13. Нажмите кнопку **Создать**. Развертывание займет около 30 минут.

### <a name="connect-the-hub-and-spoke-vnets"></a>Подключение центральной и периферийной виртуальных сетей

Теперь можно установить пиринг между центральной и периферийной виртуальными сетями.

1. Выберите группу ресурсов **FW-Manager**, а затем выберите виртуальную глобальную сеть **vwan-01**.
2. В разделе **Подключение** выберите **Подключения виртуальных сетей**.
3. Выберите команду **Добавить подключение**.
4. В поле **Имя подключения** введите **hub-spoke**.
5. В поле **Концентраторы** выберите **Hub-01**.
6. В качестве **виртуальной сети** выберите **Spoke-01**.
7. Нажмите кнопку **ОК**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Создание политики брандмауэра и защита концентратора

Политика брандмауэра определяет коллекции правил для направления трафика в один или несколько защищенных виртуальных концентраторов. Вы создадите политику брандмауэра, а затем защитите концентратор.

1. В Диспетчере брандмауэра выберите **Создание политики брандмауэра Azure**.
2. Выберите подписку и группу ресурсов **FW-Manager**.
3. В разделе **Сведения о политике** в поле **Имя** введите **Policy-01**, а в списке **Регион** выберите **Восточная часть США**.
4. Выберите **Далее: Правила**.
5. На вкладке **Правила** выберите элемент **Добавление коллекции правил**.
6. На странице **Добавление коллекции правил** в поле **Имя** введите **RC-01**.
7. В списке **Тип коллекции правил** выберите **Приложение**.
8. В поле **Приоритет** введите **100**.
9. В качестве **действия коллекции правил** должно быть выбрано **Разрешить**.
10. В поле **Имя** для правила введите **Allow-msft**.
11. В поле **Адрес источника** введите **\*** .
12. В поле **Протокол** введите **http,https**.
13. В качестве типа назначения должно быть выбрано **полное доменное имя**.
14. В поле **Назначение** введите **\*.microsoft.com**.
15. Выберите **Добавить**.
16. По завершении выберите **Next: Защищенные виртуальные концентраторы**.
17. На вкладке **Защищенные виртуальные концентраторы** выберите **Hub-01**.
19. Выберите **Review + create** (Просмотреть и создать).
20. Нажмите кнопку **Создать**.

Этот процесс может занять около пяти минут или больше.

## <a name="route-traffic-to-your-hub"></a>Маршрутизация трафика в концентратор

Теперь необходимо сделать так, чтобы сетевой трафик маршрутизировался через брандмауэр.

1. В Диспетчере брандмауэра выберите **Защищенные виртуальные концентраторы**.
2. Выберите **Hub-01**.
3. В разделе **Параметры** выберите **Параметры маршрутизации**.
4. В поле **Интернет-трафик**, **Трафик из виртуальных сетей** выберите **Отправлять через Брандмауэр Azure**.
5. В поле **Частный трафик Azure**, **Трафик в виртуальные сети** выберите **Отправлять через Брандмауэр Azure**.
6. Выберите **Изменение префиксов IP-адресов**.
7. Выберите команду **Добавить префикс IP-адреса**.
8. Введите значение **10.0.1.0/24** в качестве адреса подсети рабочей нагрузки, а затем нажмите кнопку **Сохранить**.
9. В разделе **Параметры** выберите **Подключения**.
10. Выберите подключение **hub-spoke**, после чего выберите **Защитить интернет-трафик** и нажмите кнопку **ОК**.


## <a name="test-your-firewall"></a>Тестирование брандмауэра

Чтобы протестировать правила брандмауэра, необходимо развернуть пару серверов. Вы развернете сервер Workload-Srv в подсети Workload-SN, чтобы протестировать правила брандмауэра, и сервер Jump-Srv, чтобы можно было подключиться из Интернета с помощью удаленного рабочего стола, а затем подключиться к Workload-Srv.

### <a name="deploy-the-servers"></a>Развертывание серверов

1. На портале Azure выберите **Создать ресурс**.
2. Выберите **Windows Server 2016 Datacenter** в списке **Популярные**.
3. Введите следующие значения для виртуальной машины:

   |Параметр  |Значение  |
   |---------|---------|
   |группа ресурсов.     |**FW-Manager**|
   |Имя виртуальной машины     |**Jump-Srv**|
   |Регион     |**Восточная часть США (США)**|
   |Имя пользователя для администратора     |**azureuser**|
   |Пароль     |**Azure123456!** —|

4. В разделе **Правила входящего порта** для параметра **Общедоступные входящие порты** выберите **Разрешить выбранные порты**.
5. В поле **Выбрать входящие порты** выберите **RDP (3389)** .

6. Примите остальные значения по умолчанию и щелкните **Далее: Диски**.
7. Примите значения дисков по умолчанию и щелкните **Далее: сеть**.
8. Убедитесь в том, что выбрана виртуальная сеть **Spoke-01** и подсеть **Jump-SN**.
9. Для параметра **Общедоступный IP-адрес** примите новое имя общедоступного IP-адреса по умолчанию (Jump-Srv-ip).
11. Примите остальные значения по умолчанию и щелкните **Далее: управление**.
12. Нажмите кнопку **Отключить**, чтобы выключить диагностику загрузки. Примите другие значения по умолчанию и выберите **Просмотр и создание**.
13. Просмотрите параметры на странице сводки и нажмите кнопку **Создать**.

Используйте сведения в таблице ниже, чтобы настроить еще одну виртуальную машину с именем **Workload-Srv**. Остальная конфигурация такая же, как и для виртуальной машины Srv-Jump.

|Параметр  |Значение  |
|---------|---------|
|Подсеть|**Workload-SN**|
|Общедоступный IP-адрес|**None**|
|Общедоступные входящие порты|**None**|

### <a name="add-a-route-table-and-default-route"></a>Добавление таблицы маршрутизации и маршрута по умолчанию

Чтобы разрешить подключение из Интернета к серверу Jump-Srv, необходимо создать таблицу маршрутизации и маршрут шлюза по умолчанию из подсети **Jump-SN** к Интернету.

1. На портале Azure выберите **Создать ресурс**.
2. В поле поиска введите запрос **таблица маршрутизации** и выберите элемент **Таблица маршрутизации**.
3. Нажмите кнопку **Создать**.
4. В поле **Имя** введите **RT-01**.
5. Выберите свою подписку, группу ресурсов **FW-Manager** и регион **Восточная часть США (США)** .
6. Нажмите кнопку **Создать**.
7. После завершения развертывания выберите таблицу маршрутизации **RT-01**.
8. Выберите **Маршруты**, а затем нажмите кнопку **Добавить**.
9. В поле **Имя маршрута** введите **jump-to-inet**.
10. В поле **Префикс адреса** введите **0.0.0.0/0**.
11. В поле **Тип следующего прыжка** выберите **Интернет**.
12. Нажмите кнопку **ОК**.
13. По завершении развертывания выберите **Подсети**, а затем — **Привязать**.
14. В качестве **виртуальной сети** выберите **Spoke-01**.
15. В качестве **подсети** выберите **Jump-SN**.
16. Нажмите кнопку **ОК**.

### <a name="test-the-rules"></a>Тестирование правил

Теперь проверьте правила брандмауэра, чтобы убедиться в том, что они работают должным образом.

1. На портале Azure просмотрите параметры сети для виртуальной машины **Workload-Srv** и запишите частный IP-адрес.
2. Подключите удаленный рабочий стол к виртуальной машине **Jump-Srv** и выполните вход. Откройте из нее подключение удаленного рабочего стола к частному IP-адресу **Workload-Srv**.

3. Откройте браузер Internet Explorer и перейдите на сайт [https://www.google.com](https://www.microsoft.com ).
4. Если отобразятся системы оповещения безопасности Internet Explorer, выберите **ОК** > **Закрыть**.

   Откроется домашняя страница Майкрософт.

5. Перейдите по адресу https://www.google.com.

   Брандмауэр должен вас заблокировать.

Итак, теперь вы убедились в том, что правила брандмауэра работают:

* Вы можете перейти только к одному разрешенному имени FQDN.



## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сведения о доверенных партнерах по безопасности](trusted-security-partners.md)
