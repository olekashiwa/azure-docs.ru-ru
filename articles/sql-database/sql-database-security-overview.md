---
title: Обзор безопасности
description: Дополнительные сведения о безопасности Базы данных SQL Azure и SQL Server, в том числе описание различий между облачной и локальной базой данных SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 4aa45cc1e8b79186d3ddd5d2b2964addb3929b1a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978573"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Обзор возможностей безопасности Базы данных SQL Azure

В этой статье описано, как защитить уровень данных приложения, использующего базу данных SQL Azure. Эта стратегия безопасности использует многоуровневый подход (эшелонированная защита), который представлен на следующем рисунке (уровни рассматриваются, начиная с наружного):

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Безопасность сети

База данных SQL Microsoft Azure предоставляет службу реляционной базы данных для облачных и корпоративных приложений. Чтобы защитить клиентские данные, брандмауэр запрещает сетевой доступ к серверу базы данных, кроме разрешенного явным образом по IP-адресу или виртуальной сети Azure источника трафика.

### <a name="ip-firewall-rules"></a>Правила брандмауэра для IP-адресов

Правила брандмауэра для IP-адресов предоставляют доступ к базам данным на основе исходного IP-адреса каждого запроса. См. дополнительные сведения о [правилах брандмауэра служб "База данных SQL Azure" и "Хранилище данных SQL"](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Правила брандмауэра для виртуальной сети

[Конечные точки служб для виртуальной сети](../virtual-network/virtual-network-service-endpoints-overview.md) подключают вашу виртуальную сеть к магистральной сети Azure, позволяя Базе данных SQL Azure идентифицировать подсеть виртуальной сети, из которой к ней направляется трафик. Чтобы трафик поступал в базу данных SQL Azure, используйте [теги служб](../virtual-network/security-overview.md) SQL, которые разрешают исходящий трафик через механизм групп безопасности сети.

[Правила виртуальной сети](sql-database-vnet-service-endpoint-rule-overview.md) позволяют Базе данных SQL Azure принимать только те подключения, которые отправлены из определенных подсетей в виртуальной сети.

> [!NOTE]
> Управление доступом с помощью правил брандмауэра *не* применяется к **управляемому экземпляру**. Дополнительные сведения о необходимой конфигурации сети см. в руководстве по [подключению приложения к управляемому экземпляру](sql-database-managed-instance-connect-app.md).

## <a name="access-management"></a>Управление доступом

> [!IMPORTANT]
> Управление базами данных и серверами баз данных в Azure осуществляется путем назначения ролей учетной записи пользователя портала. Дополнительные сведения об этом см. в разделе [Управление доступом на основе ролей на портале Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Проверка подлинности

Аутентификацией называют процесс подтверждения личности пользователя. База данных SQL Azure поддерживает два типа аутентификации:

- **Аутентификация SQL.**

    Аутентификация базы данных SQL выполняется на основе имени пользователя и пароля при подключении пользователя к [Базе данных SQL Azure](sql-database-technical-overview.md). При создании сервера базы данных для базы данных следует создать учетную запись администратора сервера, указав имя пользователя и пароль. Используя эти учетные данные, администратор сервера сможет выполнить аутентификацию в любой базе данных на этом сервере и получить права ее владельца. После этого администратор сервера сможет создать дополнительные имена входа и пользователей SQL, чтобы позволить пользователям подключаться с выделенными для них именами пользователей и паролями.

- **Аутентификация Azure Active Directory.**

    Аутентификация Azure Active Directory — это механизм подключения к службам [База данных SQL Azure](sql-database-technical-overview.md) и [Хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) с помощью удостоверений в Azure Active Directory (Azure AD). С помощью аутентификации Azure Active Directory администраторы могут централизованно управлять удостоверениями и разрешениями для пользователей базы данных совместно с другими службами корпорации Майкрософт. Это снижает количество хранимых паролей, а также позволяет применять централизованные политики их смены.

     Чтобы применить для Базы данных SQL аутентификацию Azure AD, необходимо создать администратора сервера, который в этом контексте называется **администратором Active Directory**. См. дополнительные сведения об [использовании аутентификации Azure Active Directory для аутентификации с помощью SQL](sql-database-aad-authentication.md). Аутентификация Azure AD поддерживает как управляемые, так и федеративные учетные записи. Федеративные учетные записи применимы для пользователей и групп Windows в пользовательских доменах, для которых настроена федерация с Azure AD.

    Кроме того, для аутентификации Azure Active Directory доступны [универсальная аутентификация Active Directory для SQL Server Management Studio](sql-database-ssms-mfa-authentication.md), которая поддерживает [многофакторную проверку подлинности](../active-directory/authentication/concept-mfa-howitworks.md), и [условный доступ](sql-database-conditional-access.md).

> [!IMPORTANT]
> Управление базами данных и серверами в Azure осуществляется путем назначения ролей учетной записи пользователя портала. Дополнительные сведения об этом см. в разделе [Управление доступом на основе ролей на портале Azure](../role-based-access-control/overview.md). Управление доступом с помощью правил брандмауэра *не* применяется к **управляемому экземпляру**. Дополнительные сведения о необходимой конфигурации сети см. в следующей статье, посвященной [подключению к управляемому экземпляру](sql-database-managed-instance-connect-app.md).

## <a name="authorization"></a>Авторизация

Авторизация — это по сути набор разрешений, которые назначены пользователю в базе данных SQL Azure и которые определяют, какие действия доступны этому пользователю. Управление разрешениями осуществляется путем добавления учетных записей пользователей в [роли базы данных](/sql/relational-databases/security/authentication-access/database-level-roles) и назначения им разрешений уровня базы данных для этих ролей или предоставления пользователю определенных [разрешений на уровне объектов](/sql/relational-databases/security/permissions-database-engine). См. дополнительные сведения об [операциях входа и пользователях](sql-database-manage-logins.md).

Рекомендуется создавать пользовательские роли при необходимости. Добавьте пользователей к роли с минимальными привилегиями, необходимыми для работы их функций. Не назначайте разрешения пользователям напрямую. Учетная запись администратора сервера является членом встроенной роли db_owner, которая обладает широкими разрешениями и должна предоставляться только некоторым пользователям с административными обязанностями. Для приложений базы данных SQL Azure используйте [инструкцию EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) , чтобы указать контекст выполнения вызванного модуля или использовать [роли приложения](/sql/relational-databases/security/authentication-access/application-roles) с ограниченными разрешениями. Такой подход гарантирует, что приложение, которое подключается к базе данных, имеет минимальные привилегии, необходимые для приложения. Следующие рекомендации также способствуют разделению обязанностей.

### <a name="row-level-security"></a>Безопасность на уровне строк

Безопасность на уровне строк позволяет клиентам контролировать доступ к строкам в таблице базы данных в зависимости от характеристик пользователя, выполняющего запрос (например, от членства в группах или контекста выполнения). Безопасность на уровне строк также может использоваться для реализации пользовательских концепций безопасности на основе меток. Дополнительные сведения см. в статье [Безопасность на уровне строк](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Защита от угроз

База данных SQL обеспечивает защиту клиентских данных, предоставляя возможности аудита и обнаружения угроз.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Аудит SQL в журналах Azure Monitor и концентраторах событий

Аудит Базы данных SQL отслеживает действия в базе данных и помогает поддерживать соответствие стандартам безопасности, сохраняя события базы данных в журнал аудита, сохраненный в учетной записи хранения Azure. Благодаря аудиту пользователи могут в реальном времени отслеживать действия в базе данных, а также анализировать и изучать действия за прошедшие периоды, чтобы выявить потенциальные угрозы, возможные нарушения и риски безопасности. См. дополнительные сведения об [аудите Базы данных SQL](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Расширенная защита от угроз анализирует журналы SQL Server, чтобы обнаружить необычное поведение и потенциально опасные попытки доступа к базам данных или их использования. Оповещения создаются для подозрительных действий, таких как атака путем внедрения кода SQL, потенциальных несанкционированного доступа данных, атак методом подбора или аномалий в шаблонах доступа для перехвата укрупнений привилегий и использования учетных данных. Оповещения просматриваются из [центра безопасности Azure](https://azure.microsoft.com/services/security-center/), где указаны сведения о подозрительных действиях, а также рекомендации по устранению угроз для дальнейшего изучения. Расширенную защиту от угроз можно включить для каждого сервера за дополнительную плату. Дополнительные сведения см. в статье [Приступая к работе с расширенной защитой угроз базы данных SQL](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Защита и шифрование информации

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Протокол TLS (шифрование при передаче)

База данных SQL защищает данные клиентов, шифруя передаваемые данные по протоколу [TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Server в любое время обеспечивает шифрование (SSL/TLS) для всех подключений. Это гарантирует, что все данные шифруются "при передаче" между клиентом и сервером независимо от значения параметра **Encrypt** или **TrustServerCertificate** в строке подключения.

Рекомендуется, чтобы в строке подключения вашего приложения вы указали зашифрованное соединение и _**не**_ доверяете сертификату сервера. Это заставляет приложение проверять сертификат сервера и, таким образом, предотвращает возможность уязвимости приложения для атак типа "злоумышленник в середине".

Например, при использовании драйвера ADO.NET это выполняется с помощью функции **Encrypt = true** и **TrustServerCertificate = false**. Строка подключения, полученная с портала Azure, будет содержать правильные параметры.

> [!IMPORTANT]
> Обратите внимание, что некоторые драйверы сторонних производителей могут не использовать TLS по умолчанию или полагаться на более старую версию TLS (< 1.2) для работы. В этом случае SQL Server по-прежнему позволяет подключаться к базе данных. Однако рекомендуется оценить риски безопасности, позволяющие таким драйверам и приложениям подключаться к базе данных SQL, особенно если вы храните конфиденциальные данные.
>
> Дополнительные сведения о TLS и подключении см. в статье [рекомендации по TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity) .

### <a name="transparent-data-encryption-encryption-at-rest"></a>Прозрачное шифрование данных (шифрование при хранении)

[Прозрачное шифрование данных (TDE) для Базы данных SQL Azure](transparent-data-encryption-azure-sql.md) добавляет дополнительный уровень безопасности, помогая защищать неактивные данные от несанкционированного или автономного доступа к необработанным файлам или резервным копиям данных. Это актуально для таких сценариев, как кража оборудования из центра обработки данных или небезопасная утилизация оборудования или носителей, например дисковых накопителей и лент с резервными копиями. TDE шифрует всю базу данных с помощью алгоритма шифрования AES, который не требует от разработчиков приложений вносить изменения в существующие приложения.

Все создаваемые в Azure базы данных SQL по умолчанию шифруются, а ключ шифрования базы данных защищается встроенным сертификатом сервера.  Обслуживание и смена сертификатов автоматически выполняются службой, не требуя действий пользователя. Если клиент предпочитает самостоятельно контролировать ключи шифрования, он может управлять ими через [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Управление ключами с помощью Azure Key Vault

Поддержка технологии [BYOK](transparent-data-encryption-byok-azure-sql.md) (создание собственных ключей) для  [прозрачного шифрования данных](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) позволяет пользователям самостоятельно управлять созданием и сменой ключей с помощью облачной системы Azure для управления внешними ключами  [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md). В случае отзыва доступа базы данных к хранилищу ключей расшифровка и считывание такой базы данных станет невозможным. Azure Key Vault предоставляет централизованную платформу для управления ключами, которая использует тщательно отслеживаемые аппаратные модули безопасности (HSM) и позволяет разделять обязанности по управлению ключами и данными, обеспечивая соответствие требованиям безопасности.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (шифрование при использовании)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

Функция [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) разработана для защиты от доступа к конфиденциальным данным, которые хранятся в определенных столбцах базы данных (например, номера кредитных карт или персональные коды, а также данные, доступ к которым предоставляется _строго ограниченному кругу лиц_). Например, администраторы баз данных или другие привилегированные пользователи имеют доступ к базе данных для выполнения задач управления, но с точки зрения бизнеса им не нужен доступ к конкретным данным в зашифрованных столбцах. Данные всегда зашифрованы и расшифровываются только для обработки в клиентских приложениях, которые имеют доступ к ключу шифрования.  Ключ шифрования никогда не предоставляется в формате SQL. Его можно хранить в [Windows Certificate Store](sql-database-always-encrypted.md) или в [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Динамическое маскирование данных

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

Динамическое маскирование данных базы данных SQL Azure ограничивает возможность раскрытия конфиденциальных данных, маскируя их для обычных пользователей. Служба динамического маскирования данных автоматически обнаруживает потенциально конфиденциальные данные в базе данных SQL Azure и предоставляет практические рекомендации по маскированию этих полей с минимальным влиянием на прикладной уровень. Эта функция работает по принципу обсфукации, скрывая конфиденциальные данные в результирующем наборе запроса по заданным полям базы данных, не меняя сами данные в базе данных. Дополнительную информацию см. в статье [Начало работы с динамическим маскированием данных в базе данных SQL (портал Azure)](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Управление безопасностью

### <a name="vulnerability-assessment"></a>Оценка уязвимостей

[Оценка уязвимостей](sql-vulnerability-assessment.md) — это легко настраиваемая служба, которая помогает обнаруживать, отслеживать и устранять потенциальные уязвимости, что позволяет предварительно повышать общую защиту базы данных. Оценка уязвимостей входит в состав предложения расширенной защиты данных (ADS), которое представляет собой унифицированный пакет расширенных средств обеспечения безопасности SQL. Доступ к службе оценки уязвимостей и управление ею можно выполнять через центральный портал SQL ADS.

### <a name="data-discovery--classification"></a>Обнаружение и классификация данных

Служба обнаружения и классификации данных (в настоящее время находится на этапе предварительной версии) предоставляет расширенные возможности, встроенные в службу "База данных SQL Azure", для обнаружения, классификации, добавления меток и защиты конфиденциальных данных в базах данных. Обнаружение и классификация конфиденциальных данных (деловых или финансовых, медицинских, персональных данных и т. д.) может играть ключевую роль в концепции защиты информации вашей организации. На основе этих процессов может формироваться инфраструктура для решения следующих задач:

- Различные сценарии безопасности, такие как мониторинг (аудит) и оповещение об аномальном доступе к конфиденциальным данным.
- Управление доступом к базам данных, содержащим конфиденциальные данные, и усиление их защиты.
- Соблюдение стандартов конфиденциальности данных и нормативных требований.

Дополнительные сведения см. в статье [Обнаружение и классификация данных в службе "База данных SQL Azure"](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Соответствие требованиям

В дополнение к приведенным выше функциям и возможностям, которые помогают выполнять требования к безопасности приложений, в отношении Базы данных SQL Azure регулярно проводится аудит, а сама база данных сертифицирована в соответствии с рядом стандартов. Дополнительные сведения см. в [центре управления безопасностью Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , где можно найти самый актуальный список сертификатов соответствия базы данных SQL.

## <a name="next-steps"></a>Дальнейшие действия

- Как пользоваться функциями контроля доступа в базе данных SQL см. в [этой статье](sql-database-control-access.md).
- Обсуждение аудита баз данных см. в разделе [Аудит базы данных SQL](sql-database-auditing.md).
- Обсуждение обнаружение угроз см. в разделе [Обнаружение угроз для базы данных SQL](sql-database-threat-detection.md).
