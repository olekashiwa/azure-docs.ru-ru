---
title: Подключение компьютеров с помощью шлюза Log Analytics | Документация Майкрософт
description: Подключите устройства и отслеживаемые Operations Manager компьютеры с помощью шлюза Log Analytics, чтобы отправить данные в службу автоматизации Azure и Log Analytics, если у них нет доступа к Интернету.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: 30854382b5a6dfd0faabfc2f59340dc21518d6f2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773287"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Подключение компьютеров без доступа к Интернету с помощью шлюза Log Analytics в Azure Monitor

>[!NOTE]
>По мере перехода Microsoft Operations Management Suite (OMS) в монитор Microsoft Azure, терминология изменяется. Эта статья относится к шлюзу OMS в качестве шлюза Azure Log Analytics. 
>

В этой статье описывается, как настроить обмен данными со службой автоматизации Azure и Azure Monitor с помощью шлюза Log Analytics, когда компьютеры, подключенные напрямую или отслеживаемые Operations Manager, не имеют доступа к Интернету. 

Шлюз Log Analytics — это прокси-сервер HTTP Forward, который поддерживает туннелирование HTTP с помощью команды HTTP CONNECT. Этот шлюз отправляет данные в службу автоматизации Azure и рабочую область Log Analytics в Azure Monitor от имени компьютеров, которые не могут напрямую подключаться к Интернету. 

Шлюз Log Analytics поддерживает:

* Создание отчетов в тех же Log Analytics рабочих областях, которые настроены на каждом агенте, и настроены с помощью гибридных рабочих ролей Runbook в службе автоматизации Azure.  
* Компьютеры Windows, на которых Microsoft Monitoring Agent напрямую подключены к рабочей области Log Analytics в Azure Monitor.
* Компьютеры Linux, на которых агент Log Analytics для Linux подключен напрямую к рабочей области Log Analytics в Azure Monitor.  
* System Center Operations Manager 2012 с пакетом обновления 1 (SP1) с UR7, Operations Manager 2012 R2 с UR3 или группой управления в Operations Manager 2016 или более поздней версии, интегрированной с Log Analytics.  

Некоторые политики безопасности ИТ не разрешают подключение к Интернету для компьютеров сети. Например, эти неподключенные компьютеры могут быть терминалами или серверами, поддерживающими ИТ-службы. Чтобы подключить эти устройства к службе автоматизации Azure или рабочей области Log Analytics, чтобы управлять ими и отслеживать их, настройте их для взаимодействия непосредственно с Log Analyticsным шлюзом. Шлюз Log Analytics может получить сведения о конфигурации и пересылать данные от их имени. Если компьютеры настроены с агентом Log Analytics для прямого подключения к рабочей области Log Analytics, то компьютеры будут взаимодействовать с Log Analytics шлюзом.  

Шлюз Log Analytics передает данные из агентов непосредственно в службу. Он не анализирует данные при передаче, а шлюз не кэширует данные при потере связи со службой. Если шлюзу не удается связаться со службой, агент продолжит работу и помещает собранные данные в очередь на диске наблюдаемого компьютера. При восстановлении соединения агент отправляет кэшированные данные, собранные в Azure Monitor.

Если группа управления Operations Manager интегрирована с Log Analytics, то серверы управления можно настроить для подключения к шлюзу Log Analytics, чтобы получать сведения о конфигурации и отправлять собранные данные в зависимости от включенного решения. .  Агенты Operations Manager отправляют данные на сервер управления. Например, агенты могут отсылать Operations Manager оповещения, данные оценки конфигурации, данные о пространстве экземпляров и данные о емкости. Другие данные большого объема, такие как журналы службы IIS (IIS), данные о производительности и события безопасности, отправляются непосредственно в шлюз Log Analytics. 

Если один или несколько серверов шлюза Operations Manager развертываются для мониторинга ненадежных систем в сети периметра или в изолированной сети, эти серверы не могут взаимодействовать с шлюзом Log Analytics.  Operations Manager серверы шлюзов могут передавать отчеты только на сервер управления.  Если группа управления Operations Manager настроена для взаимодействия с Log Analyticsным шлюзом, сведения о конфигурации прокси-сервера автоматически распределяются на каждый управляемый агентом компьютер, настроенный на получение данных журнала для Azure Monitor, даже если этот параметр пуст.

Чтобы обеспечить высокий уровень доступности для напрямую подключенных или групп управления операциями, взаимодействующих с рабочей областью Log Analytics через шлюз, используйте балансировку сетевой нагрузки (NLB) для перенаправления и распределения трафика между несколькими серверами шлюзов. Таким образом, если один сервер шлюза выйдет из строя, трафик перенаправляется на другой доступный узел.  

Компьютер, на котором работает шлюз Log Analytics, требует, чтобы Log Analytics агент Windows определял конечные точки службы, с которыми шлюз должен взаимодействовать. Агенту также необходимо направить шлюз для передачи отчетов в те же рабочие области, в которых настроены агенты или группа управления Operations Manager за шлюзом. Эта конфигурация позволяет шлюзу и агенту взаимодействовать с назначенной им рабочей областью.

Шлюз может быть многосетевым для до четырех рабочих областей. Общее число рабочих областей, поддерживаемых агентом Windows.  

Каждый агент должен иметь сетевое подключение к шлюзу, чтобы агенты могли автоматически передавать данные в шлюз и из него. Избегайте установки шлюза на контроллере домена. Компьютеры Linux, расположенные за сервером шлюза, не могут использовать метод [установки скрипта-оболочки](agent-linux.md#install-the-agent-using-wrapper-script) для установки агента log Analytics для Linux. Агент необходимо загрузить вручную, скопировать на компьютер и установить вручную, так как шлюз поддерживает взаимодействие только со службами Azure, упомянутыми ранее.

На следующей схеме показаны данные, передаваемые из прямых агентов через шлюз в службу автоматизации Azure и Log Analytics. Конфигурация прокси-сервера агента должна совпадать с портом, с которым настроен шлюз Log Analytics.  

![Схема взаимодействия Direct Agent со службами](./media/gateway/oms-omsgateway-agentdirectconnect.png)

На следующей схеме представлена передача данных из группы управления Operations Manager в Log Analytics.   

![Схема связи Operations Manager с Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Настройка системы

Компьютеры, назначенные для запуска шлюза Log Analytics, должны иметь следующую конфигурацию:

* Windows 10, Windows 8.1 или Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 или Windows Server 2008
* Microsoft .NET Framework 4.5
* По крайней мере 4-ядерный процессор и 8 ГБ памяти 
* [Агент log Analytics для Windows](agent-windows.md) , настроенный для передачи отчетов в ту же рабочую область, что и агенты, взаимодействующие через шлюз.

### <a name="language-availability"></a>Доступность языковых версий

Шлюз Log Analytics доступен на следующих языках:

- Китайский (упрощенный)
- Китайский (традиционный)
- Чешский
- Нидерландский
- Английский
- Французский
- Немецкий
- Венгерский
- Итальянский
- Японский
- Корейский
- Польский
- Португальский (Бразилия)
- Португальский (Португалия)
- Русский
- испанский (международный).

### <a name="supported-encryption-protocols"></a>Поддерживаемые протоколы шифрования

Шлюз Log Analytics поддерживает только протоколы TLS 1,0, 1,1 и 1,2.  Он не поддерживает SSL (SSL).  Чтобы обеспечить безопасность передаваемых данных в Log Analytics, настройте шлюз на использование по крайней мере TLS 1,2. Более старые версии TLS или SSL уязвимы. Хотя в настоящее время они допускают обратную совместимость, не используйте их.  

См. дополнительные сведения о [безопасной отправке данных с помощью TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Поддерживаемое число подключений агента

В следующей таблице показано приблизительное количество агентов, которые могут обмениваться данными с сервером шлюза. Поддержка основана на агентах, которые передают примерно 200 КБ данных каждые 6 секунд. Для каждого протестированного агента объем данных составляет примерно 2,7 ГБ в день.

|Шлюз |Поддерживаемые агенты (приблизительные)|  
|--------|----------------------------------|  
|ЦП: процессор Intel Xeon MP-2660 v3 \@ 2,6 ГГц 2 ядра<br> Память: 4 ГБ<br> Пропускная способность сети: 1 Гбит/с| 600|  
|ЦП: процессор Intel Xeon MP-2660 \@ v3 с частотой 2,6 ГГц, 4 ядра<br> Память: 8 ГБ<br> Пропускная способность сети: 1 Гбит/с| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Скачивание шлюза Log Analytics

Получите последнюю версию файла установки Log Analytics шлюза из центра загрузки Майкрософт ([ссылка для скачивания](https://go.microsoft.com/fwlink/?linkid=837444)) или портал Azure.

Чтобы получить шлюз Log Analytics из портал Azure, выполните следующие действия.

1. Найдите список служб и выберите **Log Analytics**. 
1. Выберите рабочую область.
1. В колонке рабочей области в разделе **Общие** выберите **Быстрый запуск**. 
1. в разделе **Выбор источника данных для подключения к рабочей области** щелкните **Компьютеры**;
1. В колонке **Direct Agent (прямой агент** ) выберите **Скачать log Analytics шлюз**.
 
   ![Снимок экрана с инструкциями по скачиванию шлюза Log Analytics](./media/gateway/download-gateway.png)

или 

1. В колонке рабочей области в разделе **Параметры** щелкните **Дополнительные параметры**.
1. Перейдите в раздел **подключенные источники** > **серверы Windows** и выберите **Скачать log Analytics шлюз**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Установка шлюза Log Analytics с помощью мастера установки

Чтобы установить шлюз с помощью мастера установки, выполните следующие действия. 

1. В папке назначения дважды щелкните файл **Log Analytics Gateway.msi**.
1. На странице **приветствия** нажмите кнопку **Далее**.

   ![Снимок экрана страницы приветствия в мастере установки шлюза](./media/gateway/gateway-wizard01.png)

1. На странице **лицензионное соглашение** выберите **я принимаю условия лицензионного соглашения** , чтобы принять условия лицензии на использование программного обеспечения корпорации Майкрософт, а затем нажмите кнопку **Далее**.
1. На странице со сведениями об **адресе порта и прокси-сервера**:

   а. Введите номер TCP-порта, который будет использоваться для шлюза. Программа установки использует этот номер порта для настройки правила входящего трафика в брандмауэре Windows.  По умолчанию используется значение 8080.
      Допустимый диапазон номера порта — от 1 до 65535. При вводе значения, которое не входит в этот диапазон, появится сообщение об ошибке.

   b. Если сервер, на котором установлен шлюз, должен взаимодействовать через прокси-сервер, введите адрес прокси-сервера, к которому должен подключаться шлюз. Например, введите `http://myorgname.corp.contoso.com:80`.  Если оставить это поле пустым, шлюз попытается подключиться к Интернету напрямую.  Если ваш прокси-сервер требует проверки подлинности, введите имя пользователя и пароль.

   c. Выберите **Далее**.

   ![Снимок экрана настройки прокси-сервера шлюза](./media/gateway/gateway-wizard02.png)

1. Если Центр обновления Майкрософт не включены, отображается страница Центр обновления Майкрософт, и вы можете включить ее. Сделайте выбор и нажмите кнопку **Далее**. В противном случае перейдите к следующему шагу.
1. На странице **Конечная папка** оставьте папку по умолчанию C:\Program c:\programfiles\oms Gateway или укажите расположение, в котором вы хотите установить шлюз. Выберите **Далее**.
1. На странице **Готово к установке** выберите **Установить**. Если контроль учетных записей пользователей запрашивает разрешение на установку, выберите **Да**.
1. После завершения установки нажмите кнопку **Готово**. Чтобы убедиться, что служба запущена, откройте оснастку Services. msc и убедитесь, что **шлюз OMS** отображается в списке служб и находится в состоянии **выполняется**.

   ![Снимок экрана с локальными службами, показывающий, что шлюз OMS работает](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Установка шлюза Log Analytics с помощью командной строки

Скачанный файл для шлюза — это установщик Windows пакет, который поддерживает автоматическую установку из командной строки или другого автоматизированного метода. Если вы не знакомы со стандартными параметрами командной строки для установщик Windows, см. раздел [Параметры командной строки](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
В следующей таблице показаны параметры, поддерживаемые программой установки.

|Параметры| Примечания|
|----------|------| 
|PORTNUMBER | Номер порта TCP для прослушивания шлюза |
|- | IP-адрес прокси-сервера |
|INSTALLDIR | Полный путь для указания каталога установки программных файлов шлюза |
|USERNAME | Идентификатор пользователя для проверки подлинности с помощью прокси-сервера |
|PASSWORD | Пароль идентификатора пользователя для проверки подлинности с помощью прокси-сервера |
|лиценсеакцептед | Укажите значение **1** , чтобы подтвердить принятие лицензионного соглашения |
|хасаус | Укажите значение **1** , если указаны параметры имени пользователя и пароля |
|хаспрокси | Укажите значение **1** при УКАЗАНИИ IP-адреса для параметра **прокси-сервера** |

Чтобы автоматически установить шлюз и настроить его с использованием определенного адреса прокси-сервера, введите следующую команду:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

При использовании параметра командной строки/Qn программа установки скрывается, а параметр/QB показывает установку во время автоматической установки.  

Если необходимо предоставить учетные данные для проверки подлинности с помощью прокси-сервера, введите следующее:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

После установки можно подтвердить, что параметры приняты (за исключением имени пользователя и пароля), с помощью следующих командлетов PowerShell:

- **Get-омсгатевайконфиг** — ВОЗВРАЩАЕТ порт TCP, на котором настроен шлюз для прослушивания.
- **Get-омсгатевайрелайпрокси** — возвращает IP-адрес прокси-сервера, с помощью которого вы настроили взаимодействие с.

## <a name="configure-network-load-balancing"></a>Настройка балансировки сетевой нагрузки

Шлюз можно настроить для обеспечения высокого уровня доступности с помощью [балансировки сетевой нагрузки (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) или аппаратных подсистем балансировки нагрузки. Подсистема балансировки нагрузки управляет трафиком, распределяя по подключенным узлам запросы на подключение, полученные от агентов Log Analytics или серверов управления Operations Manager. Если один сервер шлюза выходит из строя, трафик перенаправляется на другие узлы.

### <a name="microsoft-network-load-balancing"></a>Балансировка сетевой нагрузки (Майкрософт)

Сведения о проектировании и развертывании кластера балансировки сетевой нагрузки Windows Server 2016 см. в статье о [балансировке сетевой нагрузки](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). Ниже описана процедура настройки кластера балансировки сетевой нагрузки Майкрософт.  

1. Войдите на сервер Windows, который входит в кластер балансировки сетевой нагрузки, с использованием учетной записи администратора.  
2. Откройте диспетчер балансировки сетевой нагрузки в диспетчере сервера, щелкните **Инструменты**, а затем — **Диспетчер балансировки сетевой нагрузки**.
3. Чтобы подключить сервер шлюза Log Analytics с установленным компонентом Microsoft Monitoring Agent, щелкните правой кнопкой мыши IP-адрес кластера и выберите **Добавить узел в кластер**. 

    ![Диспетчер балансировки сетевой нагрузки — Добавление узла в кластер](./media/gateway/nlb02.png)
 
4. Введите IP-адрес сервера шлюза, который нужно подключить. 

    ![Диспетчер балансировки сетевой нагрузки — "Добавить узел в кластер: подключить"](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Сведения о проектировании и развертывании Azure Load Balancer см. в разделе [что такое Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Чтобы развернуть базовую подсистему балансировки нагрузки, выполните действия, описанные в этом [кратком руководстве](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , за исключением шагов, описанных в разделе **создание внутренних серверов**.   

> [!NOTE]
> Чтобы настроить Azure Load Balancer с помощью **SKU "базовый**", необходимо, чтобы виртуальные машины Azure принадлежали к группе доступности. Дополнительные сведения о группах доступности см. в статье [Управление доступностью виртуальных машин Windows в Azure](../../virtual-machines/windows/manage-availability.md). Чтобы добавить существующие виртуальные машины в группу доступности, обратитесь к статье [set Azure Resource Manager Availability VM Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

После создания подсистемы балансировки нагрузки необходимо создать серверный пул, который распределяет трафик на один или несколько серверов шлюзов. Выполните действия, описанные в разделе Краткое руководство по [созданию ресурсов для балансировщика нагрузки](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>При настройке проверки работоспособности необходимо настроить использование TCP-порта сервера шлюза. Проба работоспособности динамически добавляет или удаляет серверы шлюзов из вращения балансировщика нагрузки в зависимости от их реакции на проверки работоспособности. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Настройка агента Log Analytics и группы управления Operations Manager

В этом разделе вы узнаете, как настроить непосредственно подключенные агенты Log Analytics, группу управления Operations Manager или гибридные рабочие роли Runbook службы автоматизации Azure с Log Analyticsным шлюзом для взаимодействия со службой автоматизации Azure или Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Настройка автономного агента Log Analytics

При настройке агента Log Analytics замените значение прокси-сервера IP, адресом сервера шлюза Log Analytics и номером порта. Если вы развернули несколько серверов шлюзов за подсистемой балансировки нагрузки, конфигурация прокси-сервера агента Log Analytics является виртуальным IP-адресом балансировщика нагрузки.  

>[!NOTE]
>Сведения об установке агента Log Analytics на шлюзе и компьютерах Windows, которые напрямую подключаются к Log Analytics, см. в статье [Подключение компьютеров Windows к службе log Analytics в Azure](agent-windows.md). Сведения о подключении компьютеров Linux см. [в статье подключение компьютеров Linux к Azure Monitor](agent-linux.md). 
>

После установки агента на сервере шлюза настройте его для передачи информации в агенты рабочей области или рабочей области, которые взаимодействуют с этим шлюзом. Если на шлюзе не установлен агент Log Analytics Windows, событие 300 записывается в журнал событий шлюза OMS, указывая на необходимость установки агента. Если агент установлен, но не настроен для передачи отчетов в ту же рабочую область, что и агенты, которые обмениваются данными, событие 105 записывается в тот же журнал, указывая на то, что агент в шлюзе должен быть настроен для передачи в ту же рабочую область, что и агенты ммуникате с шлюзом.

После завершения настройки перезапустите службу **шлюза OMS** , чтобы применить изменения. В противном случае шлюз отклонит агенты, пытающиеся связаться с Log Analytics и сообщит о событии 105 в журнале событий шлюза OMS. Это также происходит при добавлении или удалении рабочей области из конфигурации агента на сервере шлюза.

Сведения о гибридной рабочей роли Runbook службы автоматизации см. в статье [Автоматизация ресурсов в центре обработки данных или в облаке с помощью гибридной рабочей роли Runbook](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Настройка Operations Manager, где все агенты используют один и тот же прокси-сервер

Конфигурация прокси-сервера Operations Manager применяется ко всем агентам, передающим данные в Operations Manager, автоматически, даже если соответствующий параметр не указан.  

Чтобы использовать шлюз OMS для поддержки Operations Manager, необходимо иметь следующее:

* Microsoft Monitoring Agent (версия 8.0.10900.0 или более поздняя), установленная на сервере шлюза OMS и настроенная с теми же Log Analytics рабочими областями, в которых настроена группа управления для передачи отчетов.
* Подключение к Интернету. Кроме того, шлюз OMS должен быть подключен к прокси-серверу, подключенному к Интернету.

> [!NOTE]
> Если для шлюза не указано значение, для всех агентов будут переданы пустые значения.
>

Если группа управления Operations Manager регистрируется в Log Analytics рабочей области в первый раз, вы не увидите параметр, указывающий конфигурацию прокси-сервера для группы управления в консоли. Этот параметр доступен, только если группа управления зарегистрирована в службе.  

Чтобы настроить интеграцию, обновите конфигурацию системного прокси-сервера с помощью команды Netsh в системе, в которой выполняется консоль управления, и на всех серверах управления в этой группе. Выполните следующие действия.

1. Откройте командную строку с повышенными привилегиями:

   а. Нажмите кнопку **Пуск** и введите **cmd**.  

   b. Щелкните правой кнопкой мыши пункт **Командная строка** и выберите команду **Запуск от имени администратора**.  

1. Введите следующую команду:

   `netsh winhttp set proxy <proxy>:<port>`

После завершения интеграции с Log Analytics удалите изменения, выполнив `netsh winhttp reset proxy`. Затем в консоли управления используйте параметр **настроить прокси-сервер** , чтобы указать сервер шлюза log Analytics. 

1. В консоли Operations Manager в разделе **Operations Management Suite**выберите **Подключение**, а затем выберите **настроить прокси-сервер**.

   ![Снимок экрана Operations Manager, отображающий выбор Настройка прокси-сервера](./media/gateway/scom01.png)

1. Выберите **использовать прокси-сервер для доступа к Operations Management Suite** , а затем введите IP-адрес log Analytics сервера шлюза или виртуального IP-адреса балансировщика нагрузки. Не забудьте начать с префикса `http://`.

   ![Снимок экрана Operations Manager, отображающий адрес прокси-сервера](./media/gateway/scom02.png)

1. Нажмите кнопку **Готово**. Группа управления Operations Manager настроена для обмена данными со службой Log Analytics через сервер шлюза.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Настройка Operations Manager, где конкретные агенты используют прокси-сервер

Для больших или сложных сред может потребоваться использовать сервер шлюза Log Analytics только для конкретных серверов (или групп).  Для этих серверов нельзя обновить агент Operations Manager напрямую, так как это значение перезаписывается глобальным значением для группы управления.  Вместо этого переопределите правило, используемое для отправки этих значений.  

> [!NOTE] 
> Используйте эту методику настройки, если вы хотите разрешить несколько серверов шлюза Log Analytics в вашей среде.  Например, можно указать конкретные серверы шлюза Log Analytics, которые будут указаны на региональном уровне.
>

Чтобы настроить определенные серверы или группы для использования Log Analytics сервера шлюза, выполните следующие действия. 

1. Откройте консоль Operations Manager и выберите рабочую область **Создание**.  
1. В рабочей области "Создание" выберите **Правила**. 
1. На панели инструментов Operations Manager нажмите кнопку **область** . Если эта кнопка недоступна, убедитесь, что в области " **мониторинг** " выбран объект, а не папка. Появится диалоговое окно **Ориентация объектов пакета управления** со списком общих целевых классов, групп или объектов. 
1. В поле **Искать** введите **Служба работоспособности** и выберите его из списка. Щелкните **ОК**.  
1. Поиск **правила настройки прокси-сервера Advisor**. 
1. На панели инструментов Operations Manager выберите **переопределения** , а затем наведите указатель на пункт **переопределить руле\фор для конкретного объекта класса: служба работоспособности** и выберите объект из списка.  Или создайте пользовательскую группу, содержащую объект службы работоспособности серверов, для которых вы хотите применить это переопределение. Затем примените переопределение к пользовательской группе.
1. В диалоговом окне **Свойства переопределения** добавьте флажок в столбце **переопределить** рядом с параметром **WebProxyAddress** .  В поле **значение переопределения** введите URL-адрес сервера шлюза log Analytics. Не забудьте начать с префикса `http://`.  

    >[!NOTE]
    > Вам не нужно включать правило. Он уже управляется автоматически с помощью переопределения в пакете управления переопределения безопасной ссылки Microsoft System Center Advisor, предназначенного для группы серверов мониторинга Microsoft System Center Advisor.
    > 

1. Выберите пакет управления из списка **выберите целевой пакет управления** или создайте новый незапечатанный пакет управления, выбрав **создать**. 
1. По завершении нажмите кнопку **ОК**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Настройка для гибридных рабочих ролей Runbook службы автоматизации

Если в вашей среде есть гибридные рабочие роли Runbook службы автоматизации, выполните следующие действия, чтобы настроить шлюз для поддержки рабочих ролей.

Чтобы найти URL-адрес для каждого региона, обратитесь к разделу [Настройка сети](../../automation/automation-hybrid-runbook-worker.md#network-planning) в документации по службе автоматизации.

Если компьютер зарегистрирован в качестве гибридной рабочей роли Runbook автоматически, например, если Управление обновлениями решение включено для одной или нескольких виртуальных машин, выполните следующие действия.

1. Добавьте URL-адреса службы Job Runtime Data в список разрешенных узлов в шлюзе Log Analytics. Например: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Перезапустите службу шлюза Log Analytics, используя командлет PowerShell `Restart-Service OMSGatewayService`.

Если компьютер присоединен к службе автоматизации Azure с помощью командлета регистрации гибридной рабочей роли Runbook, выполните следующие действия.

1. Добавьте URL-адрес для внесения службы агента в список разрешенных узлов в шлюзе Log Analytics. Например: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Добавьте URL-адреса службы Job Runtime Data в список разрешенных узлов в шлюзе Log Analytics. Например: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Перезапустите службу шлюза Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Полезные командлеты PowerShell

Командлеты можно использовать для выполнения задач по обновлению параметров конфигурации шлюза Log Analytics. Перед использованием командлетов обязательно выполните следующие действия.

1. Установите шлюз Log Analytics (Microsoft установщик Windows).
1. Откройте окно консоли PowerShell.
1. Импортируйте модуль, введя следующую команду: `Import-Module OMSGateway`
1. Если при выполнении предыдущего шага не произошла ошибка, значит импорт модуля выполнен успешно и вы можете использовать командлеты. Введите `Get-Module OMSGateway`
1. После использования командлетов для внесения изменений перезапустите службу шлюза OMS.

Ошибка на шаге 3 означает, что модуль не был импортирован. Эта ошибка может возникать, когда PowerShell не удается найти модуль. Модуль можно найти в пути установки шлюза OMS: *C:\Program FILES\MICROSOFT OMS гатевай\повершелл\омсгатевай*.

| **Командлет** | **Параметры** | **Описание** | **Пример** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Ключ |Получает конфигурацию службы |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Ключ (обязательно) <br> Значение |Изменяет конфигурацию службы |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Получает адрес (и учетные данные) прокси-сервера ретрансляции (вышестоящего) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Адрес<br> Имя пользователя<br> Пароль |Задает адрес (и учетные данные) прокси-сервера ретрансляции (вышестоящего) |1. Задайте прокси-сервер ретрансляции и учетные данные:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Укажите прокси-сервер ретрансляции, для которого не требуется проверка подлинности: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Очистите параметр прокси-сервера ретрансляции:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Получает разрешенный в данный момент узел (только локально настроенный разрешенный узел, не загружает разрешенные узлы автоматически) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Узел (обязательно) |Добавляет узел в список разрешенных |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Узел (обязательно) |Удаляет узел из списка разрешенных |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Субъект (обязательно) |Добавляет субъект сертификата клиента в список разрешенных |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Субъект (обязательно) |Удаляет субъект сертификата клиента из списка разрешенных |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Получает текущие разрешенные субъекты сертификата клиента (только локально настроенные разрешенные субъекты, но не автоматически скачивать разрешенные субъекты) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Устранение неисправностей

Для получения сведений о событиях, регистрируемых шлюзом, необходимо установить агент Log Analytics.

![Снимок экрана списка Просмотр событий в журнале шлюза Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Идентификаторы и описания событий шлюза Log Analytics

В следующей таблице приведены идентификаторы и описания событий для событий журнала Log Analytics шлюза.

| **Идентификатор** | **Описание** |
| --- | --- |
| 400 |Любая ошибка приложения, не имеющая конкретного идентификатора. |
| 401 |Неправильная конфигурация. Например, listenPort = "Text", а не целое число. |
| 402 |Исключение при анализе сообщений подтверждения TLS. |
| 403 |Сетевая ошибка. Например, не удается подключиться к целевому серверу. |
| 100 |Общие сведения. |
| 101 |Служба запущена. |
| 102 |Служба остановлена. |
| 103 |От клиента получена команда HTTP CONNECT. |
| 104 |Команда HTTP CONNECT не получена. |
| 105 |Целевой сервер не включен в список разрешенных, или конечный порт небезопасен (443). <br> <br> Убедитесь, что агент MMA на сервере шлюза OMS и агенты, которые обмениваются данными с шлюзом OMS, подключены к одной рабочей области Log Analytics. |
| 105 |Ошибка Ткпконнектион — недопустимый сертификат клиента: CN = Gateway. <br><br> Убедитесь, что вы используете шлюз OMS версии 1.0.395.0 или более поздней. Также убедитесь, что агент MMA на сервере шлюза OMS и агенты, взаимодействующие с шлюзом OMS, подключены к одной рабочей области Log Analytics. |
| 106 |Неподдерживаемая версия протокола TLS/SSL.<br><br> Шлюз Log Analytics поддерживает только TLS 1,0, TLS 1,1 и 1,2. Он не поддерживает протокол SSL.|
| 107 |Сеанс TLS проверен. |

### <a name="performance-counters-to-collect"></a>Счетчики производительности для собраний

В следующей таблице приведены счетчики производительности, доступные для шлюза Log Analytics. Используйте системный монитор для добавления счетчиков.

| **имя**; | **Описание** |
| --- | --- |
| Шлюз Log Analytics — Active Client Connection (Активные клиентские подключения) |Количество активных сетевых подключений клиента (TCP) |
| Шлюз Log Analytics — Error Count (Количество ошибок) |Количество ошибок |
| Шлюз Log Analytics — Connected Client (Подключенные клиенты) |Количество подключенных клиентов |
| Шлюз Log Analytics — Rejection Count (Количество отклонений) |Количество отклонений из-за любых ошибок проверки TLS |

![Снимок экрана: интерфейс шлюза Log Analytics, отображающий счетчики производительности](./media/gateway/counters.png)

## <a name="assistance"></a>Помощь

После входа в портал Azure можно получить справку по шлюзу Log Analytics или любой другой службе или функции Azure.
Чтобы получить справку, щелкните значок вопросительного знака в правом верхнем углу портала и выберите **новый запрос в службу поддержки**. Затем заполните новую форму запроса в службу поддержки.

![Снимок экрана с новым запросом на поддержку](./media/gateway/support.png)

## <a name="next-steps"></a>Дальнейшие действия

[Добавьте источники данных](../../azure-monitor/platform/agent-data-sources.md) для получения данных из подключенных источников и сохраните их в рабочей области log Analytics.
