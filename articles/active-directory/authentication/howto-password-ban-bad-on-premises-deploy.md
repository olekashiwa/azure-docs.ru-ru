---
title: Развертывание защиты паролем Azure AD. Azure Active Directory
description: Развертывание защиты паролей Azure AD для подключения недопустимых паролей в локальной среде
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61ab87a3eb1bd4b81a8e67a182a4cb6a09aa069
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888961"
---
# <a name="deploy-azure-ad-password-protection"></a>Развертывание защиты паролем Azure AD

Теперь, когда вы понимаете, [как применять защиту паролем Azure AD для Active Directory Windows Server](concept-password-ban-bad-on-premises.md), следующим шагом является планирование и выполнение развертывания.

## <a name="deployment-strategy"></a>Стратегия развертывания

Рекомендуется запускать развертывания в режиме аудита. Режим аудита является начальным параметром по умолчанию, где можно продолжать устанавливать пароли. Пароли, которые будут заблокированы, записываются в журнал событий. После развертывания прокси-серверов и агентов контроллеров домена в режиме аудита следует отслеживать влияние политики паролей на пользователей и среду при применении политики.

На этапе аудита многие организации считают, что:

* Необходимо улучшить имеющиеся операционные процессы, чтобы использовать более безопасные пароли.
* Пользователи часто используют небезопасные пароли.
* Они должны информировать пользователей о предстоящем изменении в обеспечении безопасности, возможном влиянии на них и о том, как выбрать более безопасные пароли.

Кроме того, более надежная проверка пароля может повлиять на существующую Active Directory автоматизации развертывания контроллера домена. Во время оценки периода аудита рекомендуется выполнять по крайней мере одно повышение роли контроллера домена и одно понижение контроллера домена, чтобы помочь устранить эти проблемы заранее.  Дополнительные сведения см. здесь:

* [Программе Ntdsutil. exe не удается задать пароль режима восстановления слабых служб каталогов](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Сбой повышения реплики контроллера домена из-за ненадежного режима восстановления служб каталогов](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Сбой понижения роли контроллера домена из-за ненадежного пароля локального администратора](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

После того как компонент будет запущен в режиме аудита в течение разумного периода времени, можно переключить конфигурацию с режима *аудита* на *принудительное* требование более безопасных паролей. Детальный мониторинг в это время — хорошая идея.

## <a name="deployment-requirements"></a>Требования к развертыванию

* Требования к лицензированию для защиты паролей Azure AD см. в статье [устранение недопустимых паролей в Организации](concept-password-ban-bad.md#license-requirements).
* Все компьютеры, на которых будет установлено программное обеспечение агента DC для защиты паролем Azure AD, должны работать под управлением Windows Server 2012 или более поздней версии. Это требование не подразумевает, что Active Directory домен или лес также должен находиться на функциональном уровне домена или леса Windows Server 2012. Как упоминалось в [принципах разработки](concept-password-ban-bad-on-premises.md#design-principles), не существует минимального ДФЛ или FFL, необходимого для запуска агента контроллера домена или программного обеспечения прокси-сервера.
* На всех компьютерах, на которых установлена служба агента контроллера домена, должна быть установлена платформа .NET 4,5.
* Все компьютеры, на которых будет установлена служба прокси-сервера защиты паролей Azure AD, должны работать под управлением Windows Server 2012 R2 или более поздней версии.
   > [!NOTE]
   > Развертывание службы прокси является обязательным требованием к развертыванию защиты паролей Azure AD, даже если контроллер домена может иметь исходящее прямое подключение к Интернету. 
   >
* На всех компьютерах, на которых будет установлена служба прокси-сервера защиты паролей Azure AD, должна быть установлена платформа .NET 4,7.
  Платформа .NET 4,7 уже должна быть установлена на полностью обновленной операционной системе Windows Server. При необходимости скачайте и запустите установщик, который находится на странице [автономный установщик .NET Framework 4,7 для Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* На всех компьютерах, включая контроллеры домена, на которых установлены компоненты защиты паролей Azure AD, должна быть установлена универсальная среда выполнения C. Вы можете получить среду выполнения, убедившись, что у вас есть все обновления от Центр обновления Windows. Или вы можете получить его в пакете обновления для конкретной ОС. Дополнительные сведения см. [в статье обновление для универсальной среды выполнения C в Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Сетевое подключение должно существовать между хотя бы одним контроллером домена в каждом домене и по крайней мере одним сервером, на котором размещена служба прокси-сервера для защиты паролем. Это подключение должно разрешить контроллеру домена доступ к порту сопоставителя конечных точек RPC 135 и порту сервера RPC в службе прокси. По умолчанию порт сервера RPC является динамическим портом RPC, но его можно настроить для [использования статического порта](#static).
* Все компьютеры, на которых будет установлена прокси-служба защиты паролей Azure AD, должны иметь сетевой доступ к следующим конечным точкам:

    |**Конечная точка**|**Назначение**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Запросы на аутентификацию|
    |`https://enterpriseregistration.windows.net`|Функция защиты паролем Azure AD|
 
* Предварительные требования к обновлению агента Microsoft Azure AD

  Служба обновления агента Microsoft Azure AD также устанавливается параллельно с прокси-службой защиты паролей Azure AD. Чтобы служба обновления агента Microsoft Azure AD могла функционировать, требуется дополнительная настройка:

  Если в вашей среде используется прокси-сервер HTTP, необходимо следовать рекомендациям, указанным в руководстве по [работе с существующими локальными прокси-серверами](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).

  Для службы обновления Microsoft Azure AD Connect Agent также требуются шаги TLS 1,2, указанные в [требованиях TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

  Сетевой доступ должен быть включен для набора портов и URL-адресов, указанных в [процедурах установки среды прокси приложения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

  > [!WARNING]
  > Прокси-сервер защиты паролей Azure AD и прокси приложения устанавливают разные версии службы обновления Microsoft Azure AD Connect Agent, поэтому инструкции ссылаются на содержимое прокси приложения. Эти разные версии несовместимы при установке параллельно, поэтому не рекомендуется устанавливать прокси-сервер защиты паролей Azure AD и прокси приложения параллельно на одном компьютере.

* Все компьютеры, на которых размещается служба прокси для защиты паролей, должны быть настроены на предоставление контроллерам домена возможности входа в службу прокси. Эта возможность контролируется с помощью назначения привилегий "доступ к компьютеру из сети".
* Все компьютеры, на которых размещается служба прокси для защиты паролей, должны быть настроены на разрешение исходящего трафика TLS 1,2 HTTP.
* Учетная запись глобального администратора, которая регистрирует службу прокси-сервера для защиты пароля и леса с помощью Azure AD.
* Учетная запись, которая имеет Active Directory права администратора домена в корневом домене леса для регистрации леса Windows Server Active Directory в Azure AD.
* Любой домен Active Directory, на котором выполняется программное обеспечение службы агента контроллера домена, должен использовать репликацию распределенная файловая система (DFSR) для репликации SYSVOL.

  Если в вашем домене еще не используется служба DFSR, ее необходимо перенести для использования DFSR перед установкой защиты паролей Azure AD. Дополнительные сведения см. по следующей ссылке:

  [Руководство по миграции репликации SYSVOL: FRS — репликация DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Программное обеспечение агента DC для защиты паролем Azure AD будет установлено на контроллерах домена в доменах, которые по-прежнему используют службу FRS (для репликации SYSVOL используется технология "предшественника"), но программное обеспечение не будет правильно работать в этой среде. К дополнительным отрицательным побочным эффектам относятся отдельные файлы, которые не реплицируются, и процедуры восстановления SYSVOL, которые завершаются успешно, но не выполняют автоматическую репликацию всех файлов. Необходимо выполнить миграцию домена, чтобы использовать DFSR как можно скорее, и для внутренних преимуществ DFSR, а также для разблокировки развертывания защиты паролей Azure AD. Будущие версии программного обеспечения будут автоматически отключены при работе в домене, который по-прежнему использует службу FRS.

* Служба распространения ключей должна быть включена на всех контроллерах домена, работающих под управлением Windows Server 2012. По умолчанию эта служба включена через запуск триггера вручную.

## <a name="single-forest-deployment"></a>Развертывание с одним лесом

На следующей схеме показано, как базовые компоненты защиты паролей Azure AD работают вместе в локальной среде Active Directory.

![Взаимодействие компонентов защиты паролем Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Рекомендуется проанализировать работу программного обеспечения перед его развертыванием. См. [Общие сведения о защите паролей в Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Скачать ПО

Для защиты паролей Azure AD необходимы два необходимых установщика. Они доступны в [центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Установка и Настройка службы прокси-сервера для защиты паролем

1. Выберите один или несколько серверов для размещения службы прокси-сервера для защиты паролем.
   * Каждая такая служба может предоставлять политики паролей только для одного леса. Хост-компьютер должен быть присоединен к домену в этом лесу. Поддерживаются и корневые, и дочерние домены. Необходимо сетевое подключение по крайней мере к одному КОНТРОЛЛЕРу домена в каждом домене леса и компьютера с защитой паролем.
   * Вы можете запустить службу прокси на контроллере домена для тестирования. Однако этот контроллер домена требует подключения к Интернету, что может быть проблемой безопасности. Эту конфигурацию рекомендуется использовать только для тестирования.
   * Для обеспечения избыточности рекомендуется по крайней мере два прокси-сервера. См. раздел [высокий уровень доступности](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Установите прокси-службу защиты паролей Azure AD с помощью установщика программного обеспечения `AzureADPasswordProtectionProxySetup.exe`.
   * Для этого не требуется перезагрузка. Установку программного обеспечения можно автоматизировать с использованием стандартных процедур MSI, например:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Перед установкой пакета Азуреадпассвордпротектионпроксисетуп. MSI необходимо запустить службу брандмауэра Windows, чтобы избежать ошибки установки. Если настройка брандмауэра Windows не выполняется, решение заключается в временном включении и запуске службы брандмауэра во время установки. После установки программного обеспечения прокси-сервера не определена зависимость от брандмауэра Windows. Если вы используете брандмауэр стороннего производителя, он по-прежнему должен быть настроен для удовлетворения требований к развертыванию. К ним относятся предоставление входящего доступа к порту 135 и порту прокси-сервера RPC. См. раздел [требования к развертыванию](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Откройте окно PowerShell от имени администратора.
   * Прокси-сервер защиты паролей включает новый модуль PowerShell *азуреадпассвордпротектион*. Следующие шаги запускают различные командлеты из этого модуля PowerShell. Импортируйте новый модуль следующим образом:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Чтобы проверить, запущена ли служба, используйте следующую команду PowerShell:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     В результате должно отобразиться **состояние** "работает".

1. Зарегистрируйте прокси-сервер.
   * После завершения шага 3 служба прокси-сервера выполняется на компьютере, но еще не имеет необходимых учетных данных для взаимодействия с Azure AD. Требуется регистрация в Azure AD:

     `Register-AzureADPasswordProtectionProxy`

     Для этого командлета требуются учетные данные глобального администратора для клиента Azure. Также требуются права локального Active Directory администратора домена в корневом домене леса. Также необходимо выполнить этот командлет, используя учетную запись с правами локального администратора.

     После того как эта команда будет выполнена для прокси-службы, дополнительные вызовы будут выполнены, но не нужны.

      Командлет `Register-AzureADPasswordProtectionProxy` поддерживает следующие три режима проверки подлинности. Первые два режима поддерживают многофакторную идентификацию Azure, но в третьем режиме нет. Дополнительные сведения см. в комментариях ниже.

     * Интерактивный режим проверки подлинности.

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Этот режим не работает в операционных системах Server Core. Вместо этого используйте один из следующих режимов проверки подлинности. Кроме того, этот режим может завершиться ошибкой, если включена конфигурация усиленной безопасности Internet Explorer. Чтобы устранить эту проблему, необходимо отключить эту конфигурацию, зарегистрировать прокси-сервер и снова включить ее.

     * Режим проверки подлинности по коду устройства.

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Затем завершите проверку подлинности, следуя отображаемым инструкциям на другом устройстве.

     * Режим автоматической проверки подлинности (на основе пароля).

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > В этом режиме происходит сбой, если для вашей учетной записи требуется многофакторная идентификация Azure. В этом случае используйте один из двух предыдущих режимов проверки подлинности или используйте другую учетную запись, для которой не требуется MFA.
        >
        > Если регистрация устройств Azure (которая используется в службе защиты паролем Azure AD) настроена для глобального использования MFA, вы также можете увидеть MFA. Чтобы решить эту проблему, вы можете использовать другую учетную запись, которая поддерживает MFA с одним из предыдущих режимов проверки подлинности, или временно ослабить требование регистрации устройств Azure MFA. Для этого перейдите на портал управления Azure, перейдите в раздел Azure Active Directory, устройства, затем Параметры устройства и установите для параметра "требовать многофакторную проверку подлинности присоединить устройства" к "нет". Не забудьте повторно настроить этот параметр на Да после завершения регистрации.
        >
        > Рекомендуется обходить требования MFA только в целях тестирования.

       В настоящее время не нужно указывать параметр *-форесткредентиал* , который зарезервирован для будущих функциональных возможностей.

   Регистрация службы прокси-сервера для защиты паролей необходима только один раз в течение времени существования службы. После этого прокси-служба автоматически выполнит все необходимое обслуживание.

   > [!TIP]
   > При первом запуске этого командлета для конкретного клиента Azure может возникнуть заметная задержка до завершения. Если нет сообщения о сбое, не беспокойтесь о такой задержке.

1. Зарегистрируйте лес.
   * Необходимо инициализировать локальный лес Active Directory с необходимыми учетными данными для взаимодействия с Azure с помощью командлета `Register-AzureADPasswordProtectionForest` PowerShell.

      Для этого командлета требуются учетные данные глобального администратора для клиента Azure.  Также необходимо выполнить этот командлет, используя учетную запись с правами локального администратора. Также требуются права администратора локального Active Directory предприятия. Этот шаг выполняется один раз в каждом лесу.

      Командлет `Register-AzureADPasswordProtectionForest` поддерживает следующие три режима проверки подлинности. Первые два режима поддерживают многофакторную идентификацию Azure, но в третьем режиме нет. Дополнительные сведения см. в комментариях ниже.

     * Интерактивный режим проверки подлинности.

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Этот режим не работает в операционных системах Server Core. Вместо этого используйте один из следующих двух режимов проверки подлинности. Кроме того, этот режим может завершиться ошибкой, если включена конфигурация усиленной безопасности Internet Explorer. Чтобы решить эту проблему, отключите эту конфигурацию, зарегистрируйте лес, а затем снова включите его.  

     * Режим проверки подлинности по коду устройства.

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Затем завершите проверку подлинности, следуя отображаемым инструкциям на другом устройстве.

     * Режим автоматической проверки подлинности (на основе пароля).

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > В этом режиме происходит сбой, если для вашей учетной записи требуется многофакторная идентификация Azure. В этом случае используйте один из двух предыдущих режимов проверки подлинности или используйте другую учетную запись, для которой не требуется MFA.
        >
        > Если регистрация устройств Azure (которая используется в службе защиты паролем Azure AD) настроена для глобального использования MFA, вы также можете увидеть MFA. Чтобы решить эту проблему, вы можете использовать другую учетную запись, которая поддерживает MFA с одним из предыдущих режимов проверки подлинности, или временно ослабить требование регистрации устройств Azure MFA. Для этого перейдите на портал управления Azure, перейдите в раздел Azure Active Directory, устройства, затем Параметры устройства и установите для параметра "требовать многофакторную проверку подлинности присоединить устройства" к "нет". Не забудьте повторно настроить этот параметр на Да после завершения регистрации.
        >
        > Рекомендуется обходить требования MFA только в целях тестирования.

       Эти примеры выполняются только в том случае, если вошедший в данный момент пользователь также является администратором домена Active Directory для корневого домена. Если это не так, можно указать альтернативные учетные данные домена с помощью параметра *-форесткредентиал* .

   > [!NOTE]
   > Если в вашей среде установлено несколько прокси-серверов, не имеет значения, какой прокси-сервер используется для регистрации леса.
   >
   > [!TIP]
   > При первом запуске этого командлета для конкретного клиента Azure может возникнуть заметная задержка до завершения. Если нет сообщения о сбое, не беспокойтесь о такой задержке.

   Регистрация Active Directory леса необходима только один раз в течение всего времени существования леса. После этого агенты контроллера домена в лесу будут автоматически выполнять все необходимое обслуживание. После успешного выполнения `Register-AzureADPasswordProtectionForest` для леса дополнительные вызовы командлета выполняются успешно, но не нужны.

   Для выполнения `Register-AzureADPasswordProtectionForest` в домене прокси-сервера должен быть доступен по крайней мере один контроллер домена под Windows Server 2012 или более поздней версии. До этого шага не требуется устанавливать программное обеспечение агента DC на контроллерах домена.

1. Настройте прокси-службу для защиты паролем через прокси-сервер HTTP.

   Если в среде требуется использовать конкретный прокси-сервер HTTP для взаимодействия с Azure, используйте следующий метод: Создайте файл *азуреадпассвордпротектионпрокси. exe. config* в папке%ProgramFiles%\Azure Active Directory защита паролей прокси\сервице. Включите следующее содержимое:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Если прокси-сервер HTTP требует проверки подлинности, добавьте тег *уседефаулткредентиалс* :

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   В обоих случаях замените `http://yourhttpproxy.com:8080` на адрес и порт конкретного прокси-сервера HTTP.

   Если прокси-сервер HTTP настроен для использования политики авторизации, необходимо предоставить доступ к учетной записи Active Directory компьютера компьютера, на котором размещена служба прокси-сервера, для защиты пароля.

   Рекомендуется отключить и перезапустить службу прокси-сервера после создания или обновления файла *азуреадпассвордпротектионпрокси. exe. config* .

   Прокси-служба не поддерживает использование конкретных учетных данных для подключения к прокси-серверу HTTP.

1. Необязательно. Настройте службу прокси-сервера для защиты паролем для прослушивания определенного порта.
   * Программное обеспечение агента DC для защиты паролей на контроллерах домена использует RPC через TCP для взаимодействия со службой прокси. По умолчанию служба прокси-сервера прослушивает любую доступную динамическую конечную точку RPC. Но можно настроить службу для прослушивания определенного TCP-порта, если это необходимо из-за топологии сетевых подключений или требований брандмауэра в вашей среде.
      * <a id="static" /></a>чтобы настроить запуск службы со статическим портом, используйте командлет `Set-AzureADPasswordProtectionProxyConfiguration`.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Вы должны остановить и перезапустить службу, чтобы эти изменения вступили в силу.

      * Чтобы настроить службу для работы с динамическим портом, используйте ту же процедуру, но присвойте параметру *статикпорт* значение 0:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Вы должны остановить и перезапустить службу, чтобы эти изменения вступили в силу.

   > [!NOTE]
   > Прокси-служба для защиты паролей требует перезапуска вручную после любых изменений в конфигурации порта. Но после внесения этих изменений в конфигурацию не нужно перезапускать программное обеспечение службы агента DC на контроллерах домена.

   * Чтобы запросить текущую конфигурацию службы, используйте командлет `Get-AzureADPasswordProtectionProxyConfiguration`.

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Установка службы агента контроллера домена

   Установите службу агента контроллера домена для защиты паролем с помощью пакета `AzureADPasswordProtectionDCAgentSetup.msi`.

   Для установки или удаления программного обеспечения требуется перезагрузка. Это требование связано с тем, что библиотеки DLL для фильтрации паролей загружаются или выгружаются только при перезапуске.

   Службу агента контроллера домена можно установить на компьютере, который еще не является контроллером домена. В этом случае служба будет запущена и запущена, но останется неактивной до тех пор, пока компьютер не станет контроллером домена.

   Можно автоматизировать установку программного обеспечения с помощью стандартных процедур MSI. Пример.

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Вы можете опустить флаг `/norestart`, если вы предпочитаете, чтобы установщик автоматически перезагружает компьютер.

Установка завершается после установки программного обеспечения агента DC на контроллере домена и перезагрузки компьютера. Дальнейшая настройка не требуется.

## <a name="upgrading-the-proxy-agent"></a>Обновление прокси-агента

При наличии более новой версии программного обеспечения прокси-сервера защиты паролей Azure AD обновление выполняется с помощью последней версии установщика `AzureADPasswordProtectionProxySetup.exe` программного обеспечения. Последняя версия программного обеспечения доступна в [центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=57071).

Удаление текущей версии программного обеспечения прокси-сервера не требуется — установщик выполняет обновление на месте. При обновлении программного обеспечения прокси-сервера не требуется выполнять перезагрузку. Обновление программного обеспечения может быть автоматизировано с помощью стандартных процедур MSI, например: `AzureADPasswordProtectionProxySetup.exe /quiet`.

Прокси-агент поддерживает автоматическое обновление. При автоматическом обновлении используется служба обновления агента Microsoft Azure AD Connect, которая устанавливается параллельно с прокси-службой. Автоматическое обновление включено по умолчанию и может быть включено или отключено с помощью командлета `Set-AzureADPasswordProtectionProxyConfiguration`. Текущее значение можно запросить с помощью командлета `Get-AzureADPasswordProtectionProxyConfiguration`. Корпорация Майкрософт рекомендует всегда включать параметр автоматического обновления.

Командлет `Get-AzureADPasswordProtectionProxy` можно использовать для запроса версии программного обеспечения всех установленных в настоящее время агентов прокси-сервера в лесу.

## <a name="upgrading-the-dc-agent"></a>Обновление агента контроллера домена

Если доступна более новая версия программного обеспечения агента DC для защиты паролем Azure AD, то обновление выполняется с помощью последней версии пакета `AzureADPasswordProtectionDCAgentSetup.msi` программного обеспечения. Последняя версия программного обеспечения доступна в [центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=57071).

Удаление текущей версии программного обеспечения агента контроллера домена не требуется — установщик выполняет обновление на месте. При обновлении программного обеспечения агента контроллера домена всегда требуется перезагрузка. это требование вызвано основным поведением Windows. 

Обновление программного обеспечения может быть автоматизировано с помощью стандартных процедур MSI, например: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Вы можете опустить флаг `/norestart`, если вы предпочитаете, чтобы установщик автоматически перезагружает компьютер.

Командлет `Get-AzureADPasswordProtectionDCAgent` можно использовать для запроса версии программного обеспечения всех установленных агентов контроллеров домена в лесу.

## <a name="multiple-forest-deployments"></a>Развертывание в нескольких лесах

Дополнительные требования для развертывания защиты паролем Azure AD в нескольких лесах отсутствуют. Каждый лес настроен независимо, как описано в разделе Развертывание с одним лесом. Каждый прокси-сервер защиты паролей может поддерживать только контроллеры домена из леса, к которому он присоединен. Программное обеспечение защиты паролем в любом лесу не знает о программном обеспечении защиты паролем, которое развертывается в других лесах, независимо от конфигурации доверия Active Directory.

## <a name="read-only-domain-controllers"></a>Контроллеры домена только для чтения

Изменения или наборы паролей не обрабатываются и не сохраняются на контроллерах домена только для чтения (RODC). Они пересылаются на контроллеры домена с возможностью записи. Поэтому не нужно устанавливать программное обеспечение агента контроллера домена на RODC.

## <a name="high-availability"></a>Высокий уровень доступности

Основная проблема доступности для защиты паролей — доступность прокси-серверов, когда контроллеры домена в лесу пытаются скачать новые политики или другие данные из Azure. Каждый агент контроллера домена использует простой алгоритм циклического перебора при выборе прокси-сервера для вызова. Агент пропускает прокси-серверы, которые не отвечают. Для большинства полностью подключенных Active Directory развертываний, которые имеют работоспособную репликацию каталога и состояния папки SYSVOL, достаточно двух прокси-серверов для обеспечения доступности. Это приводит к своевременной загрузке новых политик и других данных. Но можно развернуть дополнительные прокси-серверы.

Архитектура программного обеспечения агента контроллера домена устраняет обычные проблемы, связанные с высокой доступностью. Агент контроллера домена обслуживает локальный кэш самой последней скачанной политики паролей. Даже если все зарегистрированные прокси-серверы станут недоступными, агенты контроллера домена продолжают применять свои кэшированные политики паролей. Разумная частота обновления политик паролей в крупном развертывании обычно составляет дни, не часов или меньше. Таким образом, кратковременные сбои прокси-серверов не оказывают существенного воздействия на защиту паролей Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы установили службы, необходимые для защиты паролей Azure AD на локальных серверах, [выполните настройку после установки и соберите данные отчетов](howto-password-ban-bad-on-premises-operations.md) для завершения развертывания.

[Предварительная версия. Применение защиты паролем Azure AD для Windows Server Active Directory](concept-password-ban-bad-on-premises.md)
