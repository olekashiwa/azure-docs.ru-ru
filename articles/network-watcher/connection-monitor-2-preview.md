---
title: Монитор подключения (Предварительная версия) | Документация Майкрософт
description: Узнайте, как использовать монитор подключений (Предварительная версия) для мониторинга сетевой связи в распределенной среде.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 43c49cce1dd53edd5c2b13b01a31f94752579dff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169323"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>Мониторинг Объединенных подключений с помощью монитора подключений (Предварительная версия)

Монитор подключения (Предварительная версия) предоставляет единое комплексное наблюдение за сетями для гибридных и облачных развертываний Azure. Наблюдатель за сетями Azure предоставляет средства для мониторинга, диагностики и просмотра метрик, связанных с подключением, для развертываний Azure.

Основные варианты использования:

- У вас есть клиентская виртуальная машина веб-сервера, которая взаимодействует с виртуальной машиной сервера базы данных в многоуровневом приложении. Необходимо проверить сетевое подключение между двумя виртуальными машинами.
- Вы хотите, чтобы виртуальные машины в регионе Восточной части США могли проверить связь с виртуальными машинами в центральном регионе США и сравнить задержки сети между регионами
- У вас есть несколько локальных сайтов Office в городах, например в Сиэтле. подключение к URL-адресам Office 365. Вы хотите сравнить задержки пользователей с использованием URL-адресов Office 365 из Сиэтле и Эшберн.
- У вас есть гибридное приложение, которому требуется подключение к конечной точке службы хранилища Azure. Необходимо сравнить задержки между локальным сайтом и приложением Azure, подключающимся к одной конечной точке службы хранилища Azure.
- Вы хотите проверить подключение из виртуальных машин Azure, на которых размещается ваше облачное приложение, к локальным настройкам.

На этом этапе предварительной версии решение объединяет преимущества двух ключевых возможностей: [монитор подключения](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) наблюдателя за сетями и [монитор подключения службы](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)монитор производительности сети (NPM).

Изображения

* Унифицированный, интуитивно понятный интерфейс для потребностей в Azure и гибридного мониторинга
* Кросс — Мониторинг подключения между рабочими областями
* Более высокие частоты проверки и более наглядное представление о производительности сети
* Более быстрое оповещение для гибридных развертываний
* Поддержка проверок подключения на основе HTTP, TCP и ICMP
* Метрики и поддержка Log Analytics для настройки тестирования Azure и не в Azure

![Монитор подключения](./media/connection-monitor-2-preview/hero-graphic.png)

Выполните приведенные ниже действия, чтобы начать мониторинг с помощью монитора подключений (Предварительная версия).

## <a name="step-1-install-monitoring-agents"></a>Шаг 1. Установка агентов наблюдения

Монитор подключения использует упрощенные исполняемые файлы для выполнения проверок подключения.  Мы поддерживаем проверку подключения как в Azure, так и в локальной среде. Используемый исполняемый файл зависит от того, размещена ли виртуальная машина в Azure или в локальной среде.

### <a name="agents-for-azure-virtual-machines"></a>Агенты для виртуальных машин Azure

Чтобы монитор подключения распознал виртуальные машины Azure в качестве источника для мониторинга, необходимо установить расширение виртуальной машины агента наблюдателя за сетями (также известное как расширение наблюдателя за сетями). Расширение агента наблюдателя за сетями является обязательным для активации сквозного мониторинга и других расширенных функций на виртуальных машинах Azure. Вы можете [создать виртуальную машину и установить на ней расширение наблюдателя за сетями](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm).
  Кроме того, можно устанавливать, настраивать и устранять неполадки расширения наблюдателя за сетями для [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) и [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) отдельно.

Если NSG или правила брандмауэра блокируют обмен данными между источником и назначением, монитор подключения обнаружит ошибку и отобразит ее в качестве диагностического сообщения в топологии. Чтобы включить мониторинг подключения, убедитесь, что правила NSG и брандмауэра разрешают пакеты по протоколу TCP или ICMP между источником и назначением.

### <a name="agents-for-on-premise-machines"></a>Агенты для локальных компьютеров

Чтобы монитор подключения мог распознать локальные компьютеры в качестве источников для мониторинга, необходимо установить агент Log Analytics на компьютерах и включить решение для мониторинга производительности сети. Эти агенты связаны с рабочими областями Log Analytics и требуют идентификатора рабочей области и первичного ключа, прежде чем они смогут начать наблюдение.

Чтобы установить агент Log Analytics для компьютеров Windows, следуйте инструкциям, указанным в [этой ссылке](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows) .

Убедитесь, что целевой объект доступен, если в пути есть брандмауэры или устройства виртуальной сети (NVA).

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Шаг 2. Включение наблюдателя за сетями в подписке

Для всех подписок с ВИРТУАЛЬНОЙ сетью используется наблюдатель за сетями. При создании виртуальной сети в подписке наблюдатель за сетями будет включен автоматически в этом регионе и в подписке виртуальной сети. Для автоматического включения наблюдателя за сетями не влияют на ресурсы или плату за него. Убедитесь, что наблюдатель за сетями не отключен явным образом в вашей подписке. Дополнительные сведения см. в разделе [Включение наблюдателя за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>Шаг 3. Создание монитора подключения 

_Монитор подключений_ отслеживает связь через регулярные интервалы и сообщает о доступности, задержке и изменениях топологии сети между исходными агентами и конечными точками назначения. Источники могут быть виртуальными машинами Azure или локальными компьютерами, на которых установлен агент мониторинга. Конечными точками назначения могут быть URL-адреса Office 365, URL-адреса Dynamics 365, настраиваемые URL-адреса, идентификаторы ресурсов виртуальной машины Azure, IPv4, IPv6, FQDN или любое доменное имя.

### <a name="accessing-connection-monitor-preview"></a>Доступ к монитору подключений (Предварительная версия)

1. На домашней странице портал Azure перейдите в наблюдатель за сетями.
2. Щелкните вкладку "монитор подключений (Предварительная версия)" в разделе "Мониторинг" в левой области наблюдателя за сетями.
3. Вы видите все мониторы подключений, созданные с помощью интерфейса монитора подключений (Предварительная версия). Все мониторы соединений, созданные с помощью вкладки "классический интерфейс" на вкладке "монитор подключений", отображаются на вкладке "монитор подключения".

    ![Создание монитора подключения](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Создание монитора подключения

Мониторы подключений, созданные с помощью монитора подключений (Предварительная версия), предоставляют возможность добавления локальных и виртуальных машин Azure в качестве источников и мониторинга подключения к конечным точкам, которые могут охватывать Azure или любой другой URL-адрес или IP.

Ниже перечислены сущности в мониторе подключения.

* Ресурс монитора подключений — конкретный ресурс Azure для региона. Все упомянутые ниже сущности являются свойствами ресурса монитора подключения.
* Конечные точки — все источники и назначения, участвующие в проверках подключения, вызываются как конечные точки. Примеры конечных точек: виртуальные машины Azure, локальные агенты, URL-адреса
* Конфигурация теста — каждая конфигурация теста зависит от протокола. В зависимости от выбранного протокола можно определить порт, пороговые значения, частоту проверки и другие параметры.
* Группа тестирования — каждая группа тестов содержит конечные точки источника, конечные точки назначения и конфигурации тестов. Каждый монитор подключения может содержать несколько тестовых групп.
* Тест — сочетание исходной конечной точки, конечной точки назначения и тестовой конфигурации выполните один тест. Тест является самым низким уровнем, при котором доступны данные мониторинга (проверка на неудачные% и RTT)

 ![Создание монитора подключения](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>С портала

Чтобы создать монитор подключения, выполните указанные ниже действия.

1. В панели мониторинга монитора подключений (Предварительная версия) щелкните "создать" в левом верхнем углу.
2. На вкладке "основные" введите сведения для монитора подключения.
   1. Имя монитора подключения — имя монитора подключения. Здесь применяются стандартные правила именования для ресурсов Azure.
   2. Подписка — выберите подписку для монитора подключения.
   3. Регион — выберите регион для ресурса монитора подключения. Можно выбрать только исходные виртуальные машины, созданные в этом регионе.
   4. Конфигурация рабочей области. для хранения данных мониторинга можно использовать рабочую область по умолчанию, созданную монитором подключения, установив флажок по умолчанию. Чтобы выбрать настраиваемую рабочую область, снимите этот флажок. Выберите подписку и регион, чтобы выбрать рабочую область, в которой будут храниться данные мониторинга.
   5. Нажмите кнопку "Далее: Тестовые группы "Добавление тестовых групп

      ![Создание монитора подключения](./media/connection-monitor-2-preview/create-cm-basics.png)

3. На вкладке группы тестов щелкните "+ Тестовая группа", чтобы добавить тестовую группу. Используйте _Создание групп тестов в мониторе подключения_ для добавления тестовых групп. Щелкните "Проверка и создание", чтобы просмотреть монитор подключения.

   ![Создание монитора подключения](./media/connection-monitor-2-preview/create-tg.png)

4. На вкладке "Проверка + создание" ознакомьтесь с основными сведениями и группами тестирования перед созданием монитора подключения. Чтобы изменить монитор подключения в представлении "Проверка и создание", сделайте следующее:
   1. Чтобы изменить основные сведения, используйте значок карандаша, указанный в поле 1 на изображении 2.
   2. Чтобы изменить отдельные тестовые группы, щелкните группу тестирования, которую нужно изменить, чтобы открыть ее в режиме редактирования.
   3. Текущие стоимость/месяц указали затраты во время предварительной версии. Сейчас не взимается плата за использование монитора подключений, поэтому в этом столбце отображается ноль. Фактические затраты/месяц указывают на цену, которая будет платить за общедоступную доступность. Обратите внимание, что плата за прием log Analytics будет применяться даже во время предварительной версии.

5. На вкладке "Проверка + создание" нажмите кнопку "создать", чтобы создать монитор подключения.

   ![Создание монитора подключения](./media/connection-monitor-2-preview/review-create-cm.png)

6.  Монитор подключения (Предварительная версия) создаст ресурс монитора подключения в фоновом режиме.

#### <a name="from-armclient"></a>Из Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Команда развертывания:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Создание тестовых групп в мониторе подключения

Каждая группа тестов в мониторе соединений включает источники и назначение, которые проверяются на сетевых параметрах проверок, завершившихся сбоем, и на основе конфигурации тестирования.

#### <a name="from-portal"></a>С портала

Чтобы создать тестовую группу в мониторе подключения, укажите значения для указанных ниже полей.

1. Отключить тестовую группу — проверка этого поля приведет к отключению мониторинга для всех источников и назначений, указанных в тестовой группе. По умолчанию этот флажок снят.
2. Имя — имя группы тестирования.
3. Источники — в случае установки агентов в качестве источников можно указать как виртуальные машины Azure, так и локальные компьютеры. Обратитесь к шагу 1, чтобы установить агент, относящийся к источнику.
   1. Щелкните вкладку "агенты Azure", чтобы выбрать агенты Azure. Вы увидите только те виртуальные машины, которые привязаны к региону, указанному во время создания монитора подключения. Виртуальные машины по умолчанию группируются в подписку, к которой они относятся, и группы свернуты. Уровень подписки можно детализировать на другие уровни в иерархии.

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      Можно также изменить значение поля "Группировать по", чтобы начать дерево с любого другого уровня. Пример:  Group By — виртуальная сеть отобразит виртуальные машины с агентами в иерархии виртуальных сетей-\&gt; Подсети-\&gt; Виртуальные машины с агентами.

      ![Добавить источники](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Щелкните вкладку "не — агенты Azure", чтобы выбрать локальные агенты. По умолчанию агенты, сгруппированные в рабочие области, находятся в регионе. Будут перечислены только рабочие области, для которых настроено Монитор производительности сети решение. Добавьте решение "Монитор производительности сети" из [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) в рабочую область. Вы также можете использовать процесс, описанный в разделе [добавление Azure Monitor решений из коллекция решений](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) . По умолчанию вы увидите область, выбранную на вкладке Основные сведения в представлении Создание монитора подключения. Вы можете изменить регион и выбрать агенты из рабочих областей из вновь выбранного региона. Можно также изменить значение поля "Группировать по" для группировки по подсетям.

      ![Источники, не относящиеся к Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Нажмите кнопку "проверить", чтобы проверить выбранные агенты Azure и не в Azure.

      ![Обзор источников](./media/connection-monitor-2-preview/review-sources.png)

   4. По завершении выбора источников нажмите кнопку "Готово".

4. Назначения — вы можете отслеживать подключение к виртуальным машинам Azure или любой конечной точке (общедоступный IP-адрес, URL-адреса, FQDN), указав их в качестве назначений. В одной тестовой группе можно добавить виртуальные машины Azure, URL-адреса O365, URL-адреса D365 или пользовательские конечные точки.

   1. Щелкните вкладку "виртуальные машины Azure", чтобы выбрать виртуальные машины Azure в качестве назначений. По умолчанию виртуальные машины Azure, сгруппированные в иерархию подписки, находятся в том же регионе, который был выбран на вкладке Основные сведения в представлении Создание монитора подключения. Вы можете изменить регион и выбрать виртуальные машины Azure из вновь выбранного региона. Можно детализировать уровень подписки на другие уровни иерархии, такие как агенты Azure.

      ![Добавление назначений](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Добавление назначений 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Щелкните вкладку "конечные точки", чтобы выбрать конечные точки в качестве назначений. Список конечных точек будет заполнен с помощью тестовых URL-адресов Office 365 и D365, сгруппированных по имени.  Можно также выбрать конечную точку, созданную в других тестовых группах, в том же мониторе подключения. Чтобы добавить новую конечную точку, щелкните "+ конечная точка" в правом верхнем углу экрана и укажите URL-адрес конечной точки/IP/FQDN и имя

      ![Добавить конечные точки](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Нажмите кнопку "проверить", чтобы проверить выбранные агенты Azure и не в Azure.
   4. По завершении выбора источников нажмите кнопку "Готово".

5. Конфигурация теста — можно связать любое количество конфигураций тестов в заданной тестовой группе. Портал ограничено одной конфигурацией тестирования на каждую группу тестирования, но для добавления дополнительных параметров используйте Armclient.
   1. Имя — имя конфигурации теста.
   2. Протокол — можно выбрать между TCP, ICMP или HTTP. Чтобы изменить HTTP на HTTPS, выберите протокол HTTP в качестве протокола и порт 443 в качестве порта.
   3. Создание конфигурации теста сети — этот флажок отображается только в том случае, если в поле Протокол выбрано значение HTTP. Включите это поле, чтобы создать другую конфигурацию теста, используя те же источники и назначения, которые указаны на шаге 3 и 4 по протоколу TCP/ICMP. Созданная конфигурация теста называется "\&lt; Name", указанным в 5. a\&gt;\_Нетворктестконфиг "
   4. Disable traceroute (отключить) — это поле будет применимо для тестовых групп с протоколом TCP или ICMP.  Установите флажок в этом поле, чтобы предотвратить обнаружение источников топологии и время приема-передачи по прыжкам.
   5. Порт назначения — вы можете настроить это поле для размещения выбранного порта назначения.
   6. Периодичность проверки — это поле определяет, как часто источники будут проверять связь с назначениями по протоколу и порту, указанным выше. Можно выбрать время от 30 секунд, 1 минуту, 5 минут, 15 минут и 30 минут. Источники проверяют возможность подключения к местам назначения на основе выбранного значения.  Например, если выбрать 30 секунд, источники будут проверять подключение к месту назначения по крайней мере один раз в течение 30 секунд.
   7. Пороговые значения работоспособности — вы можете задать пороговые значения для сетевых параметров, указанных ниже.
      1. Проверки с ошибками в%-процент проверок не пройден, когда источники проверяют подключение к назначению по указанным выше критериям. Для протокола TCP/ICMP проверки ошибок в% могут быть эквивалентны потерям пакетов%. Для протокола HTTP это поле представляет число HTTP-запросов, которые не получили ответ.
      2. RTT в миллисекундах — время кругового пути в миллисекундах при подключении источников к назначению через конфигурацию теста, указанную выше.

      ![Добавить TG](./media/connection-monitor-2-preview/add-test-config.png)

Все источники и назначения, добавленные в тестовую группу с заданной конфигурацией теста, разбиваются на отдельные тесты. Пример:

* Тестовая группа: TG1
* Источники. 3 (A, B, C)
* Назначения. 2 (D, E)
* Конфигурация теста: 2 (конфигурация 1, конфигурация 2)
* Созданные тесты: Всего = 12

| **Номер теста** | **Source** | **Назначение** | **Имя конфигурации теста** |
| --- | --- | --- | --- |
| 1 | Объект | D | Конфигурация 1 |
| 2 | Объект | D | Конфигурация 2 |
| 3 | Объект | E | Конфигурация 1 |
| 4 | Объект | E | Конфигурация 2 |
| 5 | B | D | Конфигурация 1 |
| 6 | B | D | Конфигурация 2 |
| 7 | B | E | Конфигурация 1 |
| 8 | B | E | Конфигурация 2 |
| 9 | C | D | Конфигурация 1 |
| 10 | C | D | Конфигурация 2 |
| 11 | C | E | Конфигурация 1 |
| 12 | C | E | Конфигурация 2 |

### <a name="scale-limits"></a>Ограничения масштабирования

* Максимальное число мониторов соединений на подписку за регион — 100
* Максимальное число тестовых групп на монитор подключений — 20
* Максимальное число источников + назначение на монитор подключения — 100
* Максимальное число конфигураций тестов на монитор каждого подключения — 20 через Armclient. 2 через портал.

## <a name="step-4--data-analysis-and-alerts"></a>Шаг 4.  Анализ данных и оповещения

После создания монитора подключения источники проверяют подключение к местам назначения на основе заданной конфигурации теста.

### <a name="checks-in-a-test"></a>Проверки в тесте

На основе протокола, выбранного пользователем в конфигурации теста, монитор подключения (Предварительная версия) выполняет ряд проверок для пары назначения "источник" по выбранной частоте тестирования.

Если выбран параметр HTTP, служба вычисляет количество HTTP-ответов, которые возвращали код ответа для определения проверок, завершившихся сбоем%.  Для вычисления RTT мы измеряем время, затраченное на получение ответа HTTP-вызова.

Если выбран протокол TCP или ICMP, служба вычисляет пакет% для определения проверок, завершившихся сбоем%. Для вычисления RTT мы измеряем время, затраченное на получение подтверждения отправленных пакетов. Если вы включили данные traceroute для проверок сети, можно увидеть потери прыжков и задержки для локальной сети.

### <a name="states-of-a-test"></a>Состояния теста

В зависимости от данных, возвращаемых чеками в тесте, каждый тест может иметь следующие состояния:

* Pass =, если фактические значения для проверок со сбоем% и RTT находятся в пределах указанных пороговых значений
* Fail =, когда фактические значения для проверок не пройдены% или перекрестно заданные значения RTT. Если пороговое значение не указано, тест помечается как Failed, если проверки не пройдены% = 100%
* Предупреждение — при неудачном выполнении условий для проверок% не указан. В этом случае монитор подключения (Предварительная версия) использует автоматический заданный критерий в качестве порогового значения, а при нарушении этого порога состояние теста — "предупреждение".

### <a name="data-collection-analysis-and-alerts"></a>Сбор данных, анализ и оповещения

Все данные, собранные монитором подключения (Предварительная версия), хранятся в Log Analytics рабочей области, настроенной во время создания монитора подключения. Данные мониторинга также доступны в метриках Azure Monitor. Вы можете использовать Log Analytics, чтобы хранить данные мониторинга в течение всего времени, но по умолчанию Azure Monitor хранят метрики в течение 30 дней **.** Затем можно [задать оповещения на основе метрик для данных](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Мониторинг панелей мониторинга в решении "монитор подключений"

Вы увидите список мониторов подключения, к которым у вас есть доступ, для выбранных подписок, регионов, меток времени источника и типов назначения.

При переходе к монитору подключений (Предварительная версия) из службы "наблюдатель за сетями" вы можете **Просмотреть**:

* Монитор подключения (по умолчанию) — список всех мониторов подключения, созданных для выбранных подписок, регионов, меток времени, источника и назначения.
* Группы тестов — список всех групп тестов, созданных для выбранных подписок, регионов, меток времени, источников и типов назначения. Эти группы тестов не отфильтрованы по монитору подключения
* Тесты — список всех тестов, выполняемых для выбранных подписок, регионов, отметок времени, исходных и целевых типов. Эти тесты не фильтруются в мониторе подключения или в тестовых группах.

Каждый монитор подключения можно развернуть в тестовых группах и каждой тестовой группе в различных отдельных тестах, выполняемых в ней на панели мониторинга. Помечается как [1] на рисунке ниже.

Этот список можно отфильтровать по следующим параметрам:

* Фильтры верхнего уровня — подписки, регионы, источник метки времени и типы назначения. Помечается как [2] на рисунке ниже.
* Фильтры на основе состояния — фильтр второго уровня в состоянии монитора подключения, группы тестирования или теста. Помечается как [3] на рисунке ниже.
* Поле поиска — выберите "все", чтобы выполнить общий поиск. Для поиска по определенной сущности используйте раскрывающийся список, чтобы уменьшить результаты поиска. Помечается как [4] на рисунке ниже.

![Фильтровать тесты](./media/connection-monitor-2-preview/cm-view.png)

Пример:

1. Чтобы просмотреть все тесты для всех мониторов подключений (Предварительная версия), где Source IP = 10.192.64.56:
   1. Изменить представление на "тесты"
   2. Поиск по архивированным = 10.192.64.56
   3. Используйте раскрывающийся список рядом с полем значение, чтобы выбрать "источники"
2. Чтобы отфильтровать только невыполненные тесты для всех мониторов подключений (Предварительная версия), где Source IP = 10.192.64.56
   1. Изменить представление на "тесты"
   2. Выберите "сбой" в фильтрах на основе состояния.
   3. Поле поиска = 10.192.64.56
   4. Используйте раскрывающийся список рядом с полем значение, чтобы выбрать "источники"
3. Чтобы отфильтровать только невыполненные тесты для всех мониторов подключений (Предварительная версия), где Destination — outlook.office365.com
   1. Изменить представление на "тесты"
   2. Выберите "сбой" в фильтрах на основе состояния.
   3. Поле поиска = outlook.office365.com
   4. Используйте раскрывающийся список рядом с полем значение, чтобы выбрать "назначения"

   ![Неудачные тесты](./media/connection-monitor-2-preview/tests-view.png)

Для просмотра тенденций проверок, завершившихся сбоем,% и RTT для:

1. Монитор подключения
   1. Щелкните монитор подключения, который нужно исследовать подробно.
   2. По умолчанию данные мониторинга просматриваются по "тестовым группам".

      ![Просмотреть метрики по](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Выбор тестовой группы для подробного изучения

      ![Метрики по TG](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Вы увидите 5 основных неудачных тестов для проверок, завершившихся сбоем% или RTT МС, для тестовой группы, выбранной на предыдущем шаге. Для каждого теста вы увидите линии тренда для проверок, завершившихся сбоем%, и RTT МС
   5. Выберите тест из списка выше или выберите другой тест для подробного изучения.
   6. Для выбранного интервала времени для проверок, завершившихся сбоем%, вы увидите пороговые значения и фактические величины. Для RTT МС вы увидите пороговые значения, AVG, min и Max.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Изменение интервала времени для просмотра дополнительных данных
  8. Вы можете изменить представление на шаге b и выбрать Просмотр по источникам, назначениям или конфигурациям тестов. Затем выберите источник на основе неудачных тестов и изучите 5 основных неудачных тестов.  Пример: Выберите Просмотр по: Источники и назначения для изучения всех тестов, выполняемых между этими сочетаниями в выбранном мониторе подключений.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Тестовая группа
   1. Щелкните тестовую группу, которую необходимо подробно изучить.
   2. По умолчанию данные мониторинга просматриваются по «источнику + назначение + конфигурация тестирования (тест)».

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Выберите тест, который нужно исследовать подробно.
   4. Для выбранного интервала времени для проверок, завершившихся сбоем%, вы увидите пороговые значения и фактические величины. Для RTT МС вы увидите пороговые значения, AVG, min и Max. Вы также увидите оповещения, относящиеся к выбранному тесту.
   5. Изменение интервала времени для просмотра дополнительных данных
   6. Вы можете изменить представление на шаге b и выбрать Просмотр по источникам, назначениям или конфигурациям тестов. Затем выберите сущность для изучения 5 ведущих неудачных тестов.  Пример: Выберите Просмотр по: Источники и назначения для изучения всех тестов, выполняемых между этими сочетаниями в выбранном мониторе подключений.

3. Тест
   1. Щелкните источник + назначение + конфигурация тестирования, которые необходимо подробно изучить.
   2. Для выбранного интервала времени для проверок, завершившихся сбоем%, вы увидите пороговые значения и фактические величины. Для RTT МС вы увидите пороговые значения, AVG, min и Max. Вы также увидите оповещения, относящиеся к выбранному тесту.

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. Можно также щелкнуть "топология", чтобы просмотреть топологию сети в любой момент времени.

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. Чтобы просмотреть проблемы, обнаруженные монитором подключения, щелкните ссылку любой прыжок для сети Azure. В данный момент эта возможность недоступна для локальных сетей.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Запросы к журналу в Azure Monitor Log Analytics

Используйте Log Analytics для создания пользовательских представлений данных мониторинга. Все данные, отображаемые в пользовательском интерфейсе, заполняются из Log Analytics. Можно выполнять интерактивный анализ данных в репозитории и сопоставлять данные из различных источников, таких как работоспособность агента и другие приложения на основе Log Analytics. Можно также экспортировать данные в Excel, Power BI или ссылку с общим доступом.

#### <a name="metrics-in-azure-monitor"></a>Метрики в Azure Monitor

Для монитора подключения, созданного до работы монитора подключений (Предварительная версия), будут доступны все 4 метрики. Для мониторов соединений, созданных с помощью интерфейса монитора подключений (Предварительная версия), данные будут доступны только для метрик, помеченных как "(Предварительная версия)".

Тип ресурса — Microsoft. Network/Нетворкватчерс/Коннектионмониторс

| Метрика | Отображаемое имя метрики | Единицы | Тип статистической обработки | Описание | Измерения |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | Доля проб с ошибками, в процентах | Percent | Average | Доля проб мониторинга подключения с ошибками, в процентах | Нет измерений |
| AverageRoundtripMs | Среднее Время приема-передачи (мс) | MilliSeconds | Average | Среднее время приема-передачи (мс) в сети, вычисляемое для проб мониторинга подключения между источником и назначением |             Нет измерений |
| Чекксфаиледперцент (Предварительная версия) | % Проверок с ошибками (Предварительная версия) | Percent | Average | % проверок, завершившихся сбоем, для теста | * Коннектионмониторресаурцеид <br> * Саурцеаддресс <br> * SourceName <br> * Значение sourceresourceid <br> * SourceType <br> * Протокол <br> * Дестинатионаддресс <br> * Имя назначения <br> * Дестинатионресаурцеид <br> * DestinationType <br> * Дестинатионпорт <br> * Тестграупнаме <br> * Тестконфигуратионнаме <br> * Регион |
| Раундтриптимемс (Предварительная версия) | Время приема-передачи (МС) (Предварительная версия) | Миллисекунды | Average | Время приема-передачи (МС) для проверок, отправленных между источником и назначением. Это значение не является средним | * Коннектионмониторресаурцеид <br> * Саурцеаддресс <br> * SourceName <br> * Значение sourceresourceid <br> * SourceType <br> * Протокол <br> * Дестинатионаддресс <br> * Имя назначения <br> * Дестинатионресаурцеид <br> * DestinationType <br> * Дестинатионпорт <br> * Тестграупнаме <br> * Тестконфигуратионнаме <br> * Регион |

 ![Мониторинг метрик](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Оповещения метрик в Azure Monitor

Чтобы создать оповещение, выполните следующие действия:

1. Выбор ресурса монитора подключения, созданного с помощью монитора подключения (Предварительная версия)
2. Убедитесь, что "Метрика" отображается как тип сигнала для ресурса, выбранного на предыдущем шаге.
3. В окне Добавить условие выберите в качестве метрики имя сигнала (Чекксфаиледперцент (Предварительная версия) или Раундтриптимемс (Предварительная версия) и тип сигнала. Например: Выбор Чекксфаиледперцент (Предварительная версия)
4. Будут перечислены все измерения, применимые для каждой метрики.  Выберите имя измерения и значение измерения. Например: Выберите исходный адрес и укажите IP-адрес любого источника, связанного с ресурсом монитора подключения, выбранным на шаге 1.
5. В окне логика оповещений выберите:
   1. Тип условия — статический
   2. Условие и пороговое значение
   3. Детализация статистической обработки и частота оценки — монитор подключения (Предварительная версия) обновляет данные каждые 1 минуту.
6.  В списке действия выберите группу действий.
7. Укажите сведения о предупреждении
8. Создать правило генерации оповещений

   ![Предупреждения](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Шаг 5. Диагностика проблем в сети

Монитор подключения поможет диагностировать проблемы, связанные с ресурсом монитора подключения и в сети. Проблемы в гибридной сети будут обнаружены агентами Log Analytics, установленными на шаге 1, а проблемы в Azure будут обнаружены расширением наблюдателя за сетями.  Проблемы в гибридной сети будут отображаться на странице Диагностика, и проблемы в сети Azure будут отображаться в топологии сети.

Для сетей с локальными виртуальными машинами в качестве источников мы определим:

* Время ожидания запроса истекло
* Конечная точка не разрешается DNS-временными или постоянными. Недопустимый URL-адрес.
* Узлы не найдены.
* Источнику не удалось подключиться к назначению. Целевой объект недоступен по протоколу ICMP.
* Проблема, связанная с сертификатом. сертификат клиента, необходимый для проверки подлинности агента, список переданных сертификатов недоступен, имя узла конечной точки не совпадает с субъектом или альтернативным именем субъекта сертификата, отсутствующим корневым сертификатом в хранилище доверенных сертификатов на локальном компьютере источника, недопустимым или отозванным сертификатом SSL, несовместимым

Для сетей с виртуальными машинами Azure существуют следующие источники.

* Проблемы с агентом: работа агента остановлена, сбой разрешения DNS, нет приложения или прослушивателя, прослушивающих порт назначения, не удалось открыть сокет
* Проблемы с состоянием виртуальной машины — запуск, остановка, остановка, отмена выделения, освобождение, перезагрузка, не выделено
* Отсутствует запись таблицы ARP
* Трафик заблокирован из-за проблем с локальным брандмауэром, правила NSG
* Шлюз виртуальной сети — отсутствующие маршруты, туннель между двумя шлюзами отключен или отсутствует или не найден по туннелю, а сведения об пиринга не найдены.
* Отсутствует маршрут в MS ребра.
* Трафик остановлен из-за системных маршрутов или UDR
* BGP не включен в подключении шлюза
* Проверка DIP в Load Balancer
