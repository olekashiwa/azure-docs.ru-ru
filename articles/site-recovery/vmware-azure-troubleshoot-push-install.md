---
title: Устранение неполадок с принудительной установкой службы Mobility Service с помощью Azure Site Recovery f
description: Устранение ошибок установки служб Mobility Service при включении репликации для аварийного восстановления с Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953779"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Устранение неполадок при принудительной установке службы Mobility Service 

Установка службы Mobility Service является ключевым шагом во время включения репликации. Успех этого шага зависит исключительно от выполнения предварительных требований и работы с поддерживаемыми конфигурациями. Как правило, сбои во время установки Mobility Service возникают по следующим причинам:

* [ошибки учетных данных или привилегий](#credentials-check-errorid-95107--95108);
* [неудачные попытки входа](#login-failures-errorid-95519-95520-95521-95522);
* [Ошибки подключения](#connectivity-failure-errorid-95117--97118).
* [ошибки совместного доступа к файлам и принтерам](#file-and-printer-sharing-services-check-errorid-95105--95106);
* [ошибки WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103);
* [операционная система не поддерживается](#unsupported-operating-systems);
* [конфигурации загрузки не поддерживаются](#unsupported-boot-disk-configurations-errorid-95309-95310-95311);
* [Ошибки установки VSS](#vss-installation-failures).
* [в конфигурации GRUB указано имя устройства вместо его UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320);
* [том LVM](#lvm-support-from-920-version);
* [предупреждения о необходимости перезагрузки](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266).

При включении репликации Azure Site Recovery пытается принудительно установить агент Mobility Service на виртуальной машине. В рамках этого процесса сервер конфигурации пытается подключиться к виртуальной машине и скопировать агент. Для успешной установки выполните пошаговые инструкции по устранению неполадок, представленные ниже.

## <a name="credentials-check-errorid-95107--95108"></a>Проверка учетных данных (код ошибки: 95107 и 95108)

* Убедитесь, что учетная запись пользователя, выбранная во время включения репликации, является **допустимой и верной**.
* Azure Site Recovery необходима учетная запись **суперпользователя** или учетная запись пользователя с **правами администратора** для выполнения принудительной установки. В противном случае принудительная установка будет заблокирована на исходном компьютере.
  * В Windows (**ошибка 95107**) убедитесь, что у учетной записи есть административный доступ, локальный или для домена, на исходном компьютере.
  * Если учетная запись домена не используется, на локальном компьютере потребуется отключить контроль удаленного доступа пользователей.
    * Чтобы отключить управление удаленным доступом пользователей на компьютере, добавьте в разделе реестра HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System параметр DWORD: LocalAccountTokenFilterPolicy. Присвойте ему значение 1. Для этого в окне командной строки выполните следующую команду:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * В Linux (**ошибка 95108**) выберите суперпользователя для успешной установки агента Mobility Service. Кроме того, SFTP-службы должны работать. Включите подсистему SFTP и проверку пароля в файле sshd_config:
    1. Выполните вход в качестве привилегированного пользователя.
    2. В файле /etc/ssh/sshd_config найдите строку, которая начинается с PasswordAuthentication.
    3. Раскомментируйте строку и измените значение на yes.
    4. Найдите строку, которая начинается с Subsystem, и раскомментируйте ее.
    5. Перезапустите службу SSHD.

Если вы хотите изменить учетные данные выбранной учетной записи, следуйте инструкциям, приведенным [здесь](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Сбой недостаточных прав доступа (код ошибки: 95517)

Когда у пользователя, выбранного для установки агента Mobility Service, нет прав администратора, сервер конфигурации или сервер обработки масштабирования не смогут скопировать программное обеспечение этого агента на исходный компьютер. Таким образом, эта ошибка является результатом сбоя из-за отказа в доступе. Убедитесь, что учетной записи пользователя назначены права администратора.

Если вы хотите изменить учетные данные выбранной учетной записи, следуйте инструкциям, приведенным [здесь](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Сбой недостаточных прав доступа (код ошибки: 95518)

При сбое установки доверительных отношений между основным доменом и рабочей станцией при попытке входа на исходный компьютер Установка агента мобильности завершается сбоем с кодом ошибки 95518. Поэтому убедитесь, что учетная запись пользователя, используемая для установки агента мобильности, имеет права администратора для входа через основной домен исходного компьютера.

Если вы хотите изменить учетные данные выбранной учетной записи, следуйте инструкциям, приведенным [здесь](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Ошибки входа (код ошибки: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Учетные данные учетной записи пользователя отключены (код ошибки: 95519)

Учетная запись пользователя, выбранная во время включения репликации, была отключена. Чтобы включить учетную запись пользователя, обратитесь к [этой статье](https://aka.ms/enable_login_user) или выполните следующую команду, заменив текст *username* фактическим именем пользователя.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Учетные данные заблокированы из-за нескольких неудачных попыток входа (код ошибки: 95520)

Несколько неудачных повторных попыток при получении доступа к компьютеру приведут к блокировке учетной записи пользователя. Сбой может быть связан со следующими причинами:

* Во время настройки конфигурации указаны неверные учетные данные.
* Учетная запись пользователя, выбранная во время включения репликации, неправильная.

Поэтому измените выбранные учетные данные, следуя указанным [здесь](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) инструкциям, и повторите операцию через некоторое время.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Серверы входа недоступны на исходном компьютере (код ошибки: 95521)

Эта ошибка возникает, когда серверы входа недоступны на исходном компьютере. Недоступность серверов входа приведет к сбою запроса на вход, и, следовательно, агент Mobility Service невозможно будет установить. Для успешного входа убедитесь, что серверы входа доступны на исходном компьютере, и запустите службу входа. Подробные инструкции см. в статье KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err MSG: нет доступных серверов входа в систему.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Служба входа не запущена на исходном компьютере (код ошибки: 95522)

Служба входа не запущена на исходном компьютере, что и вызвало сбой запроса на вход. Как результат агент Mobility Service невозможно установить. Чтобы устранить проблему, убедитесь, что служба входа запущена на исходном компьютере, что гарантирует успешный вход. Для запуска службы входа выполните команду net start Logon из командной строки или запустите службу NetLogon, используя диспетчер задач.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Сбой подключения (код ошибки: 95117 & 97118)**

Сервер конфигурации или сервер обработки масштабирования пытаются подключиться к исходной виртуальной машине для установки агента Mobility Service. Эта ошибка возникает, если исходный компьютер недоступен из-за проблем с сетевым подключением. Чтобы устранить проблему:

* Проверьте связь исходного компьютера с сервером конфигурации. Если вы выбрали сервер обработки горизонтального масштабирования во время включения репликации, проверьте связь исходного компьютера с сервером обработки.
  * В командной строке компьютера исходного сервера используйте Telnet, чтобы проверить связь сервера конфигурации или сервера обработки горизонтального масштабирования с HTTPS-портом (135), как показано ниже, чтобы узнать, имеются ли проблемы с сетевым подключением или с блокировкой порта брандмауэра.

     `telnet <CS/ scale-out PS IP address> <135>`
* Кроме того, для **виртуальной машины Linux**:
  * Проверьте, установлены ли последние версии пакетов openssh, openssh-server и openssl.
  * Убедитесь, что протокол Secure Shell (SSH) включен и работает через порт 22.
  * SFTP-службы должны работать. Включите подсистему SFTP и проверку пароля в файле sshd_config:
    * Выполните вход в качестве привилегированного пользователя.
    * В файле /etc/ssh/sshd_config найдите строку, которая начинается с PasswordAuthentication.
    * Раскомментируйте строку и измените значение на yes.
    * Найдите строку, которая начинается с Subsystem, и раскомментируйте ее.
    * Перезапустите службу SSHD.
* Попытка соединения может завершиться сбоем, если после определенного периода не поступает допустимый ответ, или установленное соединение может завершиться, если подключенный узел не отвечает.
* Это может быть вызвано проблемой с подключением, сетью или доменом. Также это может быть связано с проблемой разрешения имени DNS или нехваткой порта TCP. Проверьте, существуют ли такие проблемы в вашем домене.

## <a name="connectivity-failure-errorid-95523"></a>Сбой подключения (код ошибки: 95523)

Эта ошибка возникает, если сеть, в которой находится исходный компьютер, не найдена, была удалена или больше недоступна. Единственным способом для устранения этой ошибки является проверка наличия сети.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Проверка служб совместного доступа к файлам и принтерам (код ошибки: 95105 и 95106)

После проверки подключения убедитесь, что на вашей виртуальной машине включена служба совместного доступа к файлам и принтерам. Эти параметры необходимы для копирования агента Mobility Service на исходный компьютер.

Для **Windows 2008 R2 и более ранних версий**

* Включение общего доступа к файлам и принтерам через брандмауэр Windows:
  * Откройте панель управления -> Система и безопасность -> Брандмауэр Windows -> на левой панели нажмите Дополнительные параметры -> выберите "Правила для входящих подключений" в дереве консоли.
  * Найдите правила "Общий доступ к файлам и принтерам (NB-Session-In)" и "Общий доступ к файлам и принтерам (SMB-In)". Щелкните правой кнопкой мыши на каждом правиле и нажмите **Включить правило**.
* Включение общего доступа к файлам с помощью групповой политики:
  * Откройте меню "Пуск", введите "gpmc.msc" и выполните поиск.
  * На панели навигации откройте следующие папки: "Политика локального компьютера", "Конфигурация пользователя", "Административные шаблоны", "Компоненты Windows" и "Общий доступ к сети".
  * В области сведений дважды щелкните **Запретить пользователям представлять общий доступ к файлам в профиле**. Чтобы отключить параметр "Групповая политика" и разрешить предоставлять общий доступ к файлам, нажмите "Отключено". Нажмите кнопку ОК, чтобы сохранить изменения. Дополнительные сведения см. в разделе [Включение или отключение общего доступа к файлам с помощью групповая политика](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Для **более поздних версий**следуйте инструкциям, приведенным в [статье Установка службы Mobility Service для аварийного восстановления виртуальных машин VMware и физических серверов](vmware-azure-install-mobility-service.md) , чтобы включить общий доступ к файлам и принтерам.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Проверка конфигурации инструментарий управления Windows (WMI) (WMI) (код ошибки: 95103)

После проверки служб файлов и принтеров включите службу WMI для частного и общедоступного профиля, а также профиля домена через брандмауэр. Эти параметры необходимы для запуска удаленного выполнения на исходном компьютере. Чтобы включить:

* Откройте панель управления, щелкните "Безопасность", а затем "Брандмауэр Windows".
* Откройте вкладку "Исключения" в разделе "Изменить параметры".
* В окне "Исключения" установите флажок для инструментария управления Windows (WMI), чтобы разрешить трафик WMI через брандмауэр. 

Вы также можете разрешить трафик WMI через брандмауэр в командной строке. Используйте команду `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Дополнительные сведения об устранении неполадок WMI:

* [Basic WMI testing](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) (Базовое тестирование WMI)
* [WMI Troubleshooting](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx) (Устранение неполадок WMI)
* [Устранение неполадок сценариев WMI и служб WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Неподдерживаемая операционная система

Еще одной частой причиной сбоя может быть неподдерживаемая операционная система. Убедитесь, что используете поддерживаемую версию операционной системы или ядра, для успешной установки службы Mobility Service. Избегайте использования закрытого исправления.
Чтобы просмотреть полный список версий операционных систем и ядра, которые поддерживаются службой Azure Site Recovery, см. [таблицу поддержки](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Неподдерживаемые конфигурации загрузочного диска (код ошибки: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Загрузочные и системные разделы или тома не являются одним и тем же диском (код ошибки: 95309)

До версии 9.20, если загрузочные и системные разделы или тома находились на разных дисках, такая конфигурация не поддерживалась. Начиная с [версии 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) такая конфигурация поддерживается. Используйте последнюю версию для поддержки этой возможности.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Загрузочный диск недоступен (код ошибки: 95310)

Невозможно защитить виртуальную машину без загрузочного диска. Это необходимо для обеспечения бесперебойного восстановления виртуальной машины во время восстановления после отработки отказа. Отсутствие загрузочного диска приводит к невозможности загрузки компьютера после отработки отказа. Убедитесь, что виртуальная машина содержит загрузочный диск, и повторите операцию. Также обратите внимание, что несколько загрузочных дисков на одном компьютере не поддерживаются.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>На исходном компьютере имеется несколько загрузочных дисков (код ошибки: 95311)

Виртуальная машина с несколькими загрузочными дисками не является [поддерживаемой конфигурацией](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Системный раздел на нескольких дисках (код ошибки: 95313)

До версии 9.20 конфигурация, в которой корневой раздел или том расположен на нескольких дисках, не поддерживалась. Начиная с [версии 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) такая конфигурация поддерживается. Используйте последнюю версию для поддержки этой возможности.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Не удалось включить защиту, так как имя устройства упоминается в конфигурации GRUB, а не UUID (код ошибки: 95320).

**Возможная причина:** </br>
Файлы конфигурации GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" или "/ etc / default / grub") могут содержать значение для параметров **root** и **resume** в качестве фактических имен устройств вместо UUID. Site Recovery требует наличие подхода UUID, поскольку имя устройства может меняться при перезагрузке виртуальной машины, а виртуальная машина может не выдавать одно и то же имя при сбое, что приводит к ошибкам. Пример: </br>


- Следующая строка из файла GRUB **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- Следующая строка из файла GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Обратите внимание на строки, выделенные полужирным шрифтом: параметры "root" и "resume" в GRUB содержат фактические имена устройств вместо их UUID.
 
**Как исправить:**<br>
Имена устройств должны быть заменены на соответствующий UUID.<br>


1. Найдите UUID устройства, выполнив команду "blkid \<имя устройства >". Пример:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Теперь замените имя устройства UUID в формате "root = UUID =\<UUID >". Например, если мы заменяем имена устройств UUID для параметра root и Resume, упомянутые выше в файлах "/Boot/grub2/GRUB.cfg", "/Boot/grub2/GRUB.cfg" или "/etc/default/grub:", строки в файлах будут выглядеть как. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. Перезапустите службу защиты еще раз.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Установка службы Mobility Service завершена с предупреждением о перезагрузке (код ошибки: 95265 & 95266)

Служба мобильности Site Recovery состоит из множества компонентов, один из которых — драйвер-фильтр. Драйвер-фильтр загружаться в системную память только во время перезагрузки системы. Это означает, что исправления драйвер-фильтра можно реализовать только во время загрузки нового драйвер-фильтра — только при перезагрузке системы.

**Обратите внимание**, что это предупреждение, и существующая репликация будут работать даже после обновления нового агента. Вы можете выполнить перезагрузку в любое время, когда вы хотите воспользоваться преимуществами нового драйвера фильтра, но если не перезагружать старый драйвер фильтра, он продолжает работать. В таком случае после обновления без перезагрузки можно **воспользоваться преимуществами других улучшений службы мобильности** без обновления драйвер-фильтра. Поэтому, хотя это и рекомендуется, не обязательно перезагружать компьютер после каждого обновления. Чтобы получить сведения о том, когда требуется перезагрузка, задайте [перезагрузку исходного компьютера после обновления агента мобильности](https://aka.ms/v2a_asr_reboot) в разделе обновления службы в Azure Site Recovery.

> [!TIP]
>Рекомендации по планированию обновлений в течение периода обслуживания см. в статье [Поддержка последних версий ОС и ядра](https://aka.ms/v2a_asr_upgrade_practice) в обновлениях служб в Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Поддержка диспетчера логических томов (LVM) начиная с версии 9.20

До версии 9.20 LVM поддерживался только для дисков данных. Каталог /boot должен находиться в разделе диска и не должен быть томом LVM.

Начиная с [версии 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) [LVM поддерживается для диска операционной системы](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage). Используйте последнюю версию для поддержки этой возможности.

## <a name="insufficient-space-errorid-95524"></a>Недостаточно места (код ошибки: 95524)

При копировании агента Mobility Service на исходный компьютер необходимо по крайней мере 100 МБ свободного места. Поэтому убедитесь, что у исходного компьютера достаточно свободного места, и повторите операцию.

## <a name="vss-installation-failures"></a>Ошибки установки VSS

Установка VSS является частью установки агента Mobility. Эта служба используется в процессе создания точек восстановления с согласованным состоянием приложений. Ошибки во время установки VSS могут возникнуть по нескольким причинам. Чтобы в точности определить ошибки, просмотрите файл **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. В следующем разделе выделены некоторые распространенные ошибки и действия по их устранению.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Ошибка VSS 2147023170 [0x800706BE] — код выхода 511

Эта проблема обычно возникает, когда антивирусная программа блокирует операции Azure Site Recovery служб. Действия для устранения этой проблемы.

1. Исключите все папки, упомянутые [здесь](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Следуйте указаниям, опубликованным поставщиком антивирусных программ, чтобы разблокировать регистрацию DLL в Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Ошибка VSS 7 [0x7] — код выхода 511

Эта ошибка среды выполнения вызвана нехваткой памяти для установки VSS. Убедитесь, что дисковое пространство увеличено для успешного завершения этой операции.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Ошибка VSS 2147023824 [0x80070430] — код выхода 517

Эта ошибка возникает, когда служба поставщика VSS Azure Site Recovery [помечена для удаления](https://msdn.microsoft.com/library/ms838153.aspx). Попробуйте вручную установить VSS на исходный компьютер, запустив следующую командную строку:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Ошибка VSS 2147023841 [0x8007041F] — код выхода 512

Эта ошибка возникает, когда база данных службы поставщика VSS Azure Site Recovery [заблокирована](https://msdn.microsoft.com/library/ms833798.aspx). Попробуйте вручную установить VSS на исходный компьютер, запустив следующую командную строку:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

В случае сбоя проверьте, зависает ли антивирусная программа или другие службы в состоянии "Запуск". Это может сохранить блокировку служб базы данных. Это приводит к сбоям при установке поставщика VSS. Убедитесь, что служба не находится в состоянии "starting", и повторите указанную выше операцию.

### <a name="vss-exit-code-806"></a>Код выхода VSS 806

Эта ошибка возникает, когда учетная запись пользователя, используемая для установки, не имеет разрешений на выполнение команды CSScript. Предоставьте учетной записи пользователя необходимые разрешения на выполнение скрипта и повторите операцию.

### <a name="other-vss-errors"></a>Другие ошибки VSS

Попробуйте вручную установить службу поставщика VSS на исходный компьютер, запустив следующую командную строку:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Ошибка VSS — 0x8004E00F

Эта ошибка обычно возникает во время установки агента мобильности из-за проблем в DCOM и DCOM находится в критическом состоянии.

Чтобы определить причину ошибки, используйте следующую процедуру.

**Изучите журналы установки**

1. Откройте журнал установки, расположенный по адресу К:\програмдата\асрсетуплогс\асрунифиедажентинсталлер.лог.
2. Эта проблема обозначается наличием следующей ошибки:

    Отмена регистрации существующего приложения...  Создание объекта каталога получение коллекции приложений 

    Ошибка.

    - Код ошибки:-2147164145 [0x8004E00F]
    - Код выхода: 802

Чтобы устранить проблему:

Обратитесь к [группе разработчиков платформы Microsoft Windows](https://aka.ms/Windows_Support) , чтобы получить помощь по устранению проблемы DCOM.

После устранения проблемы DCOM переустановите Azure Site Recovery поставщика VSS вручную с помощью следующей команды:
 
**C:\Program Files (x86) \Microsoft Azure site Рековери\ажент > "C:\Program Files (x86) \Microsoft Azure site Рековери\ажент\ InMageVSSProvider_Install. cmd**
  
Если согласованность приложений не является критической для требований к аварийному восстановлению, можно обойти установку поставщика VSS. 

Чтобы обойти установку поставщика VSS Azure Site Recovery и вручную установить Azure Site Recovery поставщика VSS после установки:

1. Установите службу Mobility Service. 
   > [!Note]
   > 
   > Установка завершится ошибкой на шаге "Настройка после установки". 
2. Чтобы обойти установку VSS, выполните следующие действия.
   1. Откройте каталог установки Azure Site Recovery Mobility Service, расположенный по адресу:
   
      C:\Program Files (x86) \Microsoft Azure site Рековери\ажент
   2. Измените сценарии установки поставщика VSS Azure Site Recovery **nMageVSSProvider_Install** и **InMageVSSProvider_Uninstall. cmd** , чтобы они всегда были выполнены, добавив следующие строки:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Повторно запустите установку агента Mobility Service вручную. 
4. После завершения установки и перехода к следующему шагу **Настройте**, а затем удалите добавленные строки.
5. Чтобы установить поставщик VSS, откройте командную строку от имени администратора и выполните следующую команду:
   
    **C:\Program Files (x86) \Microsoft Azure site Рековери\ажент >. \ InMageVSSProvider_Install. cmd**

9. Убедитесь, что поставщик ASR VSS установлен как служба в службах Windows, и откройте консоль MMC службы компонентов, чтобы убедиться, что в списке указан поставщик ASR VSS.
10. Если установка поставщика VSS завершается сбоем, то для устранения ошибок разрешений в CAPI2 необходимо работать с CX.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Установка поставщика VSS завершается сбоем, так как служба кластеров включена на компьютере, не являющемся кластером

Эта проблема приводит к сбою установки агента мобильности Azure Site Recovery на этапе установки поставщика VSS ASAzure Site Recovery из-за проблемы с COM+, препятствующей установке поставщика VSS.
 
### <a name="to-identify-the-issue"></a>Для выяснения проблемы

В журнале, расположенном на сервере конфигурации в К:\програмдата\асрсетуплогс\уплоадедлогс\<Дата-время > UA_InstallLogFile. log, вы увидите следующее исключение:

COM+ не удалось связаться с координатор распределенных транзакций Майкрософт (исключение из HRESULT: 0x8004E00F)

Чтобы устранить проблему:

1.  Убедитесь, что этот компьютер является не кластерным компьютером и что компоненты кластера не используются.
3.  Если компоненты не используются, удалите компоненты кластера с компьютера.

## <a name="drivers-are-missing-on-the-source-server"></a>На исходном сервере отсутствуют драйверы

Если установка агента мобильности завершается сбоем, проверьте журналы в разделе К:\програмдата\асрсетуплогс, чтобы определить, отсутствуют ли в некоторых наборах элементов необходимые драйверы.
 
Чтобы устранить проблему:
  
1. Откройте реестр с помощью редактора реестра, например regedit. msc.
2. Откройте узел HKEY_LOCAL_MACHINE \SYSTEM.
3. В узле система выберите наборы элементов управления.
4. Откройте каждый набор элементов управления и убедитесь, что имеются следующие драйверы Windows:

   - Драйвер
   - VMBus
   - Storflt
   - Storvsc
   - intelide;
 
Переустановите все отсутствующие драйверы.

## <a name="next-steps"></a>Дополнительная информация

[Узнайте, как](vmware-azure-tutorial.md) настроить аварийное восстановление для виртуальных машин VMware.
