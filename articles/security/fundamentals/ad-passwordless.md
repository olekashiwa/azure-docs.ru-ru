---
title: Проверка подлинности без пароля с помощью Azure AD
titleSuffix: Active Directory
description: Это руководством поможет генеральных, руководители, Цисос, руководитель идентификации, архитекторы предприятия и ответственные за ИТ-специалисты, отвечающие за выбор метода проверки подлинности, не защищенного паролем, для своей Azure Active Directoryной реализации.
keywords: пароль, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 5fc6a4a23573995cf791a21ec7cf3a7d68d048e8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064484"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Мир без паролей с Azure Active Directory

Пора разорвать связь с паролями. В прошлом пароли были хороши, но в современном цифровом рабочем месте они стали относительно простым вектором атаки для хакеров. Хакеры любят пользоваться паролями, поэтому нетрудно понять, почему в Azure Active Directory (Azure AD) наиболее часто отвергаются такие пароли, как год, месяц, сезон или локальная спортивная команда. Кроме того, [исследование показало](https://aka.ms/passwordguidance) , что традиционные рекомендации по управлению паролями, такие как требования к длине, требования к сложности и частоте изменений, понизит производительность по различным причинам, связанным с человеческим персоналом.

Три типа атак, обычно используемых для взлома учетных записей пользователей, — это распыление паролей, фишинг и воспроизведение нарушений. Такие функции Azure AD, как [интеллектуальная блокировка](../../active-directory/authentication/howto-password-smart-lockout.md), [запрещенные пароли](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)и [Защита паролем](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) , могут помочь защититься от атак такого рода. Аналогичным образом, реализация [многофакторной проверки подлинности](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) или двухфакторной проверки подлинности обеспечивает дополнительную безопасность, запрашивая вторую форму аутентификации. Но в долгосрочном запуске решение без пароля является лучшим решением для обеспечения наиболее безопасного метода проверки подлинности.

Эта статья является началом поездки, помогающим понять и реализовать решения Майкрософт, не использующие пароли, и помочь вам выбрать один или несколько из следующих вариантов:

* **Windows Hello для бизнеса**. В Windows 10 пароли Windows Hello для бизнеса заменяют строгую двухфакторную проверку подлинности на компьютерах и мобильных устройствах. Эта проверка подлинности состоит из нового типа учетных данных пользователя, привязанного к устройству и использующего биометрическое распознавание или ПИН-код.

* **Вход без пароля с Microsoft Authenticator**. Приложение Microsoft Authenticator можно использовать для входа в учетную запись Azure AD без использования пароля. Подобно технологии Windows Hello для бизнеса, Microsoft Authenticator использует проверку подлинности на основе ключей для включения учетных данных пользователя, привязанных к устройству и использующего биометрическую метрику или ПИН-код.

* **Ключи безопасности FIDO2**. FIDO2 предоставляет учетные данные для входа шифрования, уникальные для каждого веб-сайта и хранящиеся на локальном устройстве, например Windows Hello или внешние ключи безопасности. Эти ключи безопасности защищены от угроз фишинга, кражи пароля и атак с помощью воспроизведения. В сочетании с проверкой пользователей с помощью биометрических или ПИН-кода решение является двухфакторной проверкой на соответствие современным требованиям безопасности.

## <a name="the-future-of-passwordless-authentication"></a>Будущее проверки подлинности с паролем

Эти дни, банки, компании кредитных карт и другие организации и веб-службы часто защищают вашу учетную запись. для этого требуется проверить удостоверение дважды: один раз, используя пароль, а затем снова по телефону, тексту или приложению. Хотя многофакторная идентификация позволяет устранить проблемы безопасности паролей, которые являются общими, украденными или догадкными, это не устраняет неудобство в попытке запомнить их. Пользователи и организации, которые хотят в сегодняшней облачной эпохе, являются надежными *и* удобными методами проверки подлинности с незащищенными паролями.

![Удобство и безопасность](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Windows Hello для бизнеса, без пароля, с учетом Microsoft Authenticator и ключей безопасности FIDO2 все совместно используют простую общую архитектуру, предоставляющую пользователям метод проверки подлинности, который очень безопасен и удобен в использовании. Все три основаны на технологии открытого и закрытого ключей, требуется локальный жест, например биометрические метрики или ПИН-код, и закрытые ключи, привязанные к одному устройству и безопасно хранящиеся и не являющиеся общими.

## <a name="windows-hello-for-business"></a>Windows Hello для бизнеса

В Windows 10 пароли Windows Hello для бизнеса заменяют строгую двухфакторную проверку подлинности на компьютерах и устройствах. Проверка подлинности состоит из нового типа учетных данных пользователя, привязанного к устройству и использующего биометрический жест или ПИН-код, который позволяет пользователям проходить проверку подлинности в Azure AD, а также локально Active Directory. Простое вход в устройство с помощью Windows Hello для бизнеса — это просто. Вы используете либо закрепление, либо биометрические жесты, такие как отпечатки пальца или распознавания лиц.

### <a name="windows-hello-for-business-scenarios"></a>Сценарии Windows Hello для бизнеса

Windows Hello для бизнеса идеально подходит для информационных работников, имеющих свой назначенный компьютер под управлением Windows. Биометрическая метрика и учетные данные напрямую привязаны к компьютеру пользователя, что предотвращает доступ других пользователей, кроме владельца. Благодаря интеграции PKI и встроенной поддержке единого входа (SSO) Windows Hello для бизнеса предоставляет простой и удобный способ легкого доступа к корпоративным ресурсам в локальной среде и в облаке.

### <a name="windows-hello-for-business-deployment-considerations"></a>Рекомендации по развертыванию Windows Hello для бизнеса

Windows Hello для бизнеса — это распределенная система, использующая несколько компонентов для выполнения регистрации, подготовки и проверки подлинности устройств. Поэтому развертывание требует надлежащего планирования для нескольких команд в Организации. С помощью этого средства можно принять решение о типе развертывания Windows Hello для бизнеса и [возможностях](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) , которые необходимо учитывать.

Существует множество параметров, которые можно выбрать при развертывании Windows Hello для бизнеса. Предоставление нескольких вариантов позволяет практически любой организации развернуть Windows Hello для бизнеса. Рассмотрим следующие типы поддерживаемых развертываний:

* [Развертывание доверия к гибридному развертыванию ключей в Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Развертывание гибридного доверия сертификата, присоединенного к Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Руководства по развертыванию единого входа в Azure AD присоединяются](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Развертывание доверия с локальным ключом](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Развертывание локального доверия сертификатов](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Предоставление множества параметров делает развертывание несложным. Однако большинство организаций, скорее всего, будут определять, что они уже реализовали большую часть инфраструктуры, от которой зависит развертывание Windows Hello для бизнеса. В любом случае важно понимать, что Windows Hello для бизнеса — это распределенная система, и рекомендуется использовать правильное планирование.

Мы рекомендуем ознакомиться с разрешениями по [развертыванию Windows Hello для бизнеса](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) , чтобы выбрать модель развертывания, которая лучше всего подходит для конкретной организации. Затем, основываясь на планировании, ознакомьтесь с руководством по [развертыванию Windows Hello для бизнеса](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) , чтобы обеспечить успешное развертывание Windows Hello для бизнеса в существующей среде.

### <a name="how-windows-hello-for-business-works"></a>Работа с Windows Hello для бизнеса

#### <a name="user-sets-up-windows-hello-for-business"></a>Пользователь настраивает Windows Hello для бизнеса

После первой двухфакторной проверки подлинности пользователя во время регистрации Windows Hello настраивается на устройстве пользователя, и Windows запрашивает у пользователя указание жеста, который может быть биометрической метрикой, такой как отпечаток пальца или распознавания лиц, или ПИН-код. После установки пользователь предоставляет жест для проверки их подлинности. Windows использует Windows Hello для проверки подлинности пользователей.

В зависимости от возможностей устройства Windows 10 вы будете иметь встроенную безопасную анклава, известную как доверенный платформенный модуль (TPM) или программный доверенный платформенный модуль. Доверенный платформенный модуль хранит закрытый ключ, для его разблокировки требуется либо лицо, либо отпечаток, либо ПИН-код. Биометрические данные не перемещаются и никогда не отправляются на внешние устройства или серверы. Нет ни одной точки сбора, которую злоумышленник может ослабить для кражи биометрических данных, так как Windows Hello сохраняет на устройстве только биометрические данные.

> [!TIP]
> На этой поверхности ПИН-код похож на пароль, но на самом деле он безопаснее. Важное различие между паролем и ПИН-кодом заключается в том, что ПИН-код привязан к конкретному устройству, на котором оно было настроено. Пользователь, отправивший ваш пароль, может войти в свою учетную запись из любого места. Но если бы они украсть ваш ПИН-код, им пришлось бы украсть ваше физическое устройство. Более того, поскольку ПИН-код является локальным для устройства, он не передается в любом месте, поэтому он не может быть перехвачен при передаче или краже от сервера.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Пользователь, использующий Windows Hello для бизнеса для входа

В биометрии и ПИН-кодах Windows Hello для бизнеса используется асимметричное шифрование (открытый/закрытый ключ) для проверки подлинности. Во время проверки подлинности шифрование привязано к ключу сеанса TLS, который защищает процесс проверки подлинности таким образом, чтобы атака "злоумышленник в середине" (MiTM) не могла украсть получившийся маркер безопасности или артефакт и воспроизводить их из других мест.

Windows Hello для бизнеса — это удобный процесс входа в систему для проверки подлинности пользователя в Azure AD и Active Directory ресурсов. Устройства, присоединенные к Azure AD, проходят аутентификацию в Azure во время входа в систему и при необходимости могут пройти проверку подлинности для Active Directory. Гибридные Azure Active Directory присоединенные устройства проходят проверку подлинности Active Directory во время входа и проходят проверку подлинности для Azure Active Directory в фоновом режиме.

![Просмотреть исходное изображение](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

Следующие шаги иллюстрируют проверку подлинности входа для Azure Active Directory.

![Экран блокировки Windows 10](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. Пользователь входит в Windows с помощью биометрического или ЗАКРЕПЛЕНного жеста. Жест разблокирует закрытый ключ Windows Hello для бизнеса и отправляется в облачный поставщик поддержки безопасности для проверки подлинности, который называется поставщиком облачного контроля доступа.

2. Поставщик AP облака запрашивает nonce из Azure Active Directory.

3. Azure AD возвращает nonce, действительный в течение 5 минут.

4. Поставщик AP облака подписывает nonce с помощью закрытого ключа пользователя и возвращает подписанный элемент nonce в Azure Active Directory.

5. Azure Active Directory проверяет подписанный элемент nonce, используя безопасный зарегистрированный открытый ключ пользователя, в соответствии с сигнатурой nonce. После проверки подписи Azure AD проверяет возвращаемый знак nonce. После проверки nonce Azure AD создает PRT с ключом сеанса, который шифруется в ключе транспорта устройства, и возвращает его поставщику AP облака.

6. Поставщик AP облака получает зашифрованный PRT с ключом сеанса. С помощью закрытого ключа транспорта устройства поставщик AP облака расшифровывает сеансовый ключ и защищает сеансовый ключ с помощью TPM устройства.

7. Поставщик AP облака возвращает успешное реагирование на проверку подлинности для Windows, после чего пользователь может получить доступ к Windows, а также к облачным и локальным приложениям без повторной проверки подлинности (SSO).

Более подробный обзор процесса проверки подлинности в других сценариях, включающих Windows Hello для бизнеса, см. в статье [Windows Hello для бизнеса и проверка подлинности](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Пользователь управляет своими учетными данными Windows Hello для бизнеса

[Службы сброса ПИН-кода Майкрософт](/windows/security/identity-protection/hello-for-business/hello-feature-pin-reset) — это функция в Azure AD, которая позволяет пользователям сбрасывать свой ПИН-код при необходимости. Используя групповую политику, Microsoft Intune или совместимую MDM, администратор может настроить устройства Windows 10 для безопасного использования службы сброса ПИН-кода Майкрософт, которая позволяет пользователям сбрасывать забытый ПИН-код через параметры или выше экрана блокировки без необходимости Повторная регистрация.

Иногда пользователям приходится возвращаться к использованию паролей. [Самостоятельный сброс пароля](../../active-directory/authentication/howto-sspr-deployment.md) (SSPR) — это еще одна функция Azure AD, которая позволяет пользователям сбрасывать пароли без необходимости обращения к ИТ-специалистам. Прежде чем использовать службу, пользователи должны зарегистрироваться в службе самостоятельного сброса пароля или зарегистрировать их. Во время регистрации пользователь выбирает один или несколько методов проверки подлинности, включенных в Организации. SSPR позволяет пользователям быстро получить разблокирование и продолжить работу независимо от того, где они находятся или на время суток. Разрешая разблокирование пользователей, Организация может сократить непроизводительное время и высокую стоимость поддержки для большинства распространенных проблем, связанных с паролями.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Вход без пароля с помощью Microsoft Authenticator

Вход без пароля с помощью Microsoft Authenticator — еще одно решение без пароля, которое можно использовать для входа в учетные записи Azure AD с помощью входа с использованием телефона. Вы по-прежнему должны проверить вашу личность, предоставив что-то, что вам известно и что у вас есть, но вход с помощью телефона позволяет пропустить ввод пароля и выполнить проверку личности на мобильном устройстве с помощью отпечатка пальца, лица или ПИН-кода.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator сценарии, не требующие пароля

Приложение Microsoft Authenticator позволяет пользователям проверить удостоверение и пройти проверку подлинности для своей рабочей или личной учетной записи. Его также можно использовать для дополнения пароля с одноразовым секретным кодом или push-уведомлением или замены пароля. Вместо пароля пользователи подтверждают свою личность с помощью мобильного телефона при сканировании отпечатков пальцев, лица или IRI или ПИН-кода. Это средство, основанное на защищенных технологиях, аналогично тому, как используется Windows Hello, является упакованным в простое приложение на мобильном устройстве, что делает его удобным вариантом для пользователей. Приложение Microsoft Authenticator доступно для Android и iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Рекомендации по развертыванию Microsoft Authenticator

Предварительные требования для использования приложения Microsoft Authenticator для входа без пароля в Azure AD включают в себя следующее:

* Для конечных пользователей включена многофакторная идентификация Azure

* Пользователям рекомендуется регистрировать свои устройства с помощью Microsoft Intune или стороннего решения для управления мобильными устройствами (MDM), чтобы упростить развертывание приложения.

Предполагая, что эти требования соблюдены, администраторы включают в клиенте функцию входа без пароля с помощью [Windows PowerShell.](../../active-directory/authentication/howto-authentication-passwordless-phone.md) Когда вход по телефону включен в клиенте, конечные пользователи могут выбрать вход с помощью телефона, выбрав рабочую или учебную учетную запись на экране **учетных записей** приложения, а затем выбрав **включить вход с помощью телефона**.

Если вход без пароля включен администратором, конечные пользователи должны удовлетворять следующим требованиям.

* Зарегистрировано в службе многофакторной идентификации Azure

* Последняя версия Microsoft Authenticator установлена на устройствах под управлением iOS 8,0 или более поздней версии или Android 6,0 или выше

* Рабочая или учебная учетная запись с Push-уведомлениями, добавленными в приложение

Чтобы избежать возможного блокирования учетной записи или необходимости повторного создания учетных записей на новом устройстве, рекомендуется использовать Microsoft Authenticator для [резервного копирования учетных данных учетной записи](../../active-directory/user-help/user-help-auth-app-backup-recovery.md) в облако. Создав резервную копию, вы сможете с помощью этого же приложения восстановить эту информации на другое устройство. Это избавит вас от проблем с блокировкой или повторным созданием учетных записей.

Поскольку большинство пользователей привыкли использовать только пароли для проверки подлинности, важно, чтобы ваша организация обучена пользователям об этом процессе. Осведомленность может снизить вероятность того, что пользователи вызывают службу поддержки для [устранения любых проблем](../../active-directory/authentication/howto-authentication-passwordless-phone.md) , связанных с входом с помощью приложения Microsoft Authenticator.

> [!NOTE]
> Потенциальная точка отказа этого решения заключается в том, что перемещаемый пользователь находится в расположении, где нет подключения к Интернету. FIDO2 ключи безопасности и Windows Hello для бизнеса не подвержены тем же ограничениям.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Как выполняется вход без пароля с помощью Microsoft Authenticator

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Пользователь настраивает вход без пароля с помощью Microsoft Authenticator

Прежде чем Microsoft Authenticator приложение можно будет использовать в качестве решения без пароля для входа в учетную запись Azure AD, действия должны выполняться как администратором, так и конечным пользователем.

Во-первых, администратору необходимо [включить использование приложения в качестве учетных данных](../../active-directory/authentication/howto-authentication-passwordless-phone.md) в клиенте с помощью Windows PowerShell. Администратор также должен включить для пользователей многофакторную идентификацию Azure (Azure MFA) и настроить приложение Microsoft Authenticator в качестве одного из [методов проверки](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods).

Конечным пользователям потребуется [скачать и установить](../../active-directory/user-help/user-help-auth-app-download-install.md) приложение Microsoft Authenticator и [настроить свою учетную запись](../../active-directory/user-help/security-info-setup-auth-app.md) для использования Microsoft Authenticator приложения в качестве одного из методов проверки.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Пользователь, использующий Microsoft Authenticator для входа без пароля

Приложение Microsoft Authenticator позволяет войти в любую учетную запись Azure AD, не используя пароль. Хотя экран блокировки Windows 10 не включает Microsoft Authenticator приложение в качестве параметра входа, пользователи по-прежнему могут ввести свое имя пользователя, а затем получить push-уведомление на мобильном устройстве для проверки присутствия. Пользователи подтверждают свое присутствие, сопоставляя номер на экране входа, а затем выполняя проверку лица, отпечаток или ПИН-код, чтобы разблокировать закрытый ключ и выполнить проверку подлинности. Этот метод многофакторной проверки более безопасен, чем пароль, и является более удобным, чем ввод пароля и кода.

![Вход в систему проверки подлинности](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Проверка подлинности без пароля с использованием Microsoft Authenticator соответствует тому же базовому шаблону, что и Windows Hello для бизнеса, но немного сложнее, так как необходимо идентифицировать пользователя, чтобы служба Azure AD могла найти версию приложения Microsoft Authenticator используется.

![Процесс проверки подлинности](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. Пользователь вводит свое имя пользователя.

2. Azure AD определяет, что у пользователя есть надежные учетные данные, и запускает надежный поток учетных данных.

3. Уведомление отправляется в приложение через Cлужба push-уведомлений Apple (APNS) на устройствах iOS или через Firebase Cloud Messaging (FCM) на устройствах Android.

4. Пользователь получает push-уведомление и открывает приложение.

5. Приложение вызывает Azure AD и получает запрос на подтверждение присутствия и nonce.

6. Пользователь завершает задачу, вводя биометрические метрики или ПИН-код для разблокировки закрытого ключа.

7. Элемент nonce подписывается закрытым ключом и отправляется обратно в Azure AD.

8. Azure AD выполняет проверку открытого и закрытого ключа и возвращает маркер.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Пользователь управляет входом без пароля с учетными данными Microsoft Authenticator

Благодаря [Объединенной регистрации](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)пользователи могут регистрироваться и получать преимущества многофакторной идентификации Azure и самостоятельного сброса пароля. Пользователи регистрируют эти параметры и управляют ими, перейдя к [странице Мой профиль](https://aka.ms/mysecurityinfo). Помимо включения SSPR, Объединенная регистрация поддерживает несколько методов и действий проверки подлинности.

## <a name="fido2-security-keys"></a>Ключи безопасности FIDO2

FIDO2 — это последняя версия FIDO Alliance Standard, которая имеет два компонента — стандарт веб-аутентификации предложенной W3C (WebAuthN) и соответствующий протокол FIDO Alliance Client-to-Authenticator (CTAP2). Стандарты FIDO2 позволяют пользователям использовать средства проверки подлинности на основе оборудования, мобильных устройств и биометрических данных, чтобы легко пройти проверку подлинности с помощью многих приложений и веб-сайтов в мобильных и настольных средах.

Корпорация Майкрософт и отраслевые партнеры работали вместе на устройствах безопасности FIDO2 для Windows Hello, чтобы обеспечить простую и безопасную проверку подлинности на общих устройствах. Ключи безопасности FIDO2 позволяют вам передавать свои учетные данные и безопасно пройти проверку подлинности на подключенном к [Azure AD](https://aka.ms/azuread418)устройстве Windows 10, входящем в вашу организацию.

Процесс WebAuthN определяет API, который обеспечивает разработку и внедрение надежной проверки подлинности с паролем для веб-приложений и служб. Протокол CTAP позволяет внешним устройствам, таким как совместимые с FIDO ключи безопасности, работать с WebAuthN и служить в качестве средств проверки подлинности. С помощью веб-аутентификации пользователи могут входить в веб-службы с помощью их лиц, отпечатка, ПИН-кода или переносимых ключей безопасности FIDO2, используя надежные учетные данные открытого ключа вместо паролей. В настоящее время WebAuthN поддерживается в Microsoft ребро, а поддержка Chrome и Firefox — в разработке.

Устройства и маркеры, которые соответствуют протоколам FIDO2, WebAuthN и CTAP, предоставляют межплатформенное решение строгой проверки подлинности без использования паролей. Партнеры корпорации Майкрософт работают с различными конструктивными факторами безопасности, например с ключами безопасности USB и смарт-картами с поддержкой NFC.

### <a name="fido2-security-keys-scenarios"></a>Сценарии с ключами безопасности FIDO2

Ключи безопасности FIDO2 можно использовать для входа в Azure AD, выбрав ключ безопасности в качестве поставщика учетных данных на экране блокировки Windows 10. Имя пользователя или пароль не требуются, что делает его идеальным решением для тех работников, которые совместно используют компьютеры с несколькими пользователями. Они также являются превосходным вариантом проверки подлинности, когда корпоративные политики определяют, что учетные данные пользователя должны быть физически отделены от их устройства. Пользователи также могут выбрать вход на веб-сайты с помощью ключа безопасности FIDO2 в браузере Microsoft ребра в Windows 10 версии 1809 или более поздней.

### <a name="fido2-security-keys-deployment-considerations"></a>Рекомендации по развертыванию ключей безопасности FIDO2

Администраторы могут включить поддержку FIDO2 в Azure AD и назначить эту возможность пользователям или группам. Кроме того, можно создавать политики для подготовки ключей и настраивать такие ограничения, как разрешение или блокирование определенного набора аппаратных ключей безопасности. Ключи должны быть физически распространены для конечных пользователей.

**Требования для включения входа без пароля в Azure AD и веб-сайты с помощью ключей безопасности FIDO2 включают следующее.**

* Azure AD

* Многофакторная идентификация Azure

* Обзор Объединенной регистрации

* Для FIDO2 Security Key repreview требуется совместимый ключ безопасности FIDO2

* Для веб-аутентификации (WebAuthN) требуется Microsoft ребро в Windows 10 версии 1809 или более поздней

* Для входа в Windows на основе FIDO2 требуется Azure AD, присоединенный к Windows 10 версии 1809 или более поздней (наилучший опыт — Windows 10 версии 1903 или более поздней).

FIDO2-совместимые конструктивные факторы включают устройства USB, NFC и Bluetooth. Мы рекомендуем выбрать форм-фактор, соответствующий вашим потребностям, так как некоторые платформы и браузеры еще не совместимы с FIDO2.

Кроме того, рекомендуется, чтобы каждая организация была создана протоколом для пользователей и администраторов, которые должны следовать при потере или краже ключа безопасности. Пользователи должны сообщить о утерянном или украденном ключе, чтобы администраторы или пользователь могли удалять ключи безопасности из профиля пользователя и подготавливать новый.

### <a name="how-fido2-security-keys-works"></a>Принципы работы ключей безопасности FIDO2

#### <a name="user-sets-up-fido2-security-key"></a>Пользователь настраивает ключ безопасности FIDO2

Хотя администраторы могут [вручную подготавливать ключи](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) и распределять их конечным пользователям, подготовка и включение поставщика учетных данных FIDO2 на экране блокировки Windows 10 будет поддерживаться в [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Администраторам также потребуется использовать [портал Azure](https://portal.azure.com/) для включения аппаратных устройств маркеров в качестве метода проверки подлинности без пароля.

Для развертывания ключей безопасности FIDO2 также требуется, чтобы пользователи регистрировали свои ключи, используя [объединенную регистрацию](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md). Благодаря Объединенной регистрации пользователи регистрируются один раз и получают преимущества многофакторной идентификации Azure и сброса пароля с помощью единого входа (SSPR).

Помимо выбора маркера оборудования в качестве метода многофакторной проверки подлинности по умолчанию, рекомендуется также выбрать дополнительный параметр проверки.

* Microsoft Authenticator--уведомление

* Код приложения или маркера оборудования для проверки подлинности

* Телефонный звонок

* Текстовое сообщение

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Пользователь, использующий ключ безопасности FIDO2 для входа

FIDO2 предоставляет уровень абстракции между форм-фактором, используемым в качестве средства проверки подлинности, и шифрование открытого и закрытого ключей, чтобы включить встроенные средства проверки подлинности платформы, такие как Windows Hello и ключи безопасности, для разрешения в закрытый ключ и предоставления открытого ключа, который можно использовать в качестве идентификатора для доступа к внешним ресурсам. Ключи безопасности FIDO2 оснащены собственными встроенными безопасными анклава, в которых хранится закрытый ключ, и для их разблокировки требуются биометрические метрики или ПИН-код. Учетные данные нельзя повторно использовать, воспроизводить или совместно использоваться в разных службах, а также не подвергаться фишингу и MiTM атакам или нарушениям серверов.

![Вход в FIDO2](./media/ad-passwordless/azure-ad-pwdless-image6.png)

Ключи безопасности FIDO2 обеспечивают безопасную проверку подлинности независимо от конструктивных параметров. Ключ безопасности содержит учетные данные и должен быть защищен с помощью дополнительного второго фактора, такого как отпечаток (интегрированный в ключ безопасности) или ПИН-кода, который должен быть введен при входе в Windows. Партнеры корпорации Майкрософт работают с различными форм-факторами ключа безопасности. Некоторые примеры включают ключи безопасности USB и смарт-карты с поддержкой NFC.

> [!NOTE]
> Для обеспечения [совместимости с Майкрософт](https://aka.ms/fido2securitykeys)ключ безопасности должен реализовывать определенные функции и расширения из протокола FIDO2 CTAP. Корпорация Майкрософт проверила эти решения для обеспечения совместимости с Windows 10 и Azure Active Directory.

![Процесс входа в FIDO2](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. Пользователь подключает устройство FIDO2 к компьютеру.

2. Windows обнаруживает ключ безопасности FIDO2.

3. Windows отправляет запрос на проверку подлинности.

4. Azure AD отправляет сервер nonce.

5. Пользователь завершает свой жест для разблокировки закрытого ключа, хранящегося в Secure анклава ключа безопасности FIDO2.

6. Ключ безопасности FIDO2 подписывает nonce с помощью закрытого ключа.

7. Запрос маркера PRT с подписанным nonce отправляется в Azure AD.

8. Azure AD проверяет подписанный nonce с помощью открытого ключа FIDO2.

9. Azure AD возвращает PRT, чтобы разрешить доступ к локальным ресурсам.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Пользователь управляет учетными данными ключа безопасности FIDO2

Как и в случае с Microsoft Authenticatorным приложением, Управление учетными данными для ключей безопасности FIDO2 использует объединенные возможности регистрации для конечных пользователей.

## <a name="deciding-a-passwordless-method"></a>Выбор метода, не защищенного паролем

Выбор между этими тремя вариантами защиты от пароля зависит от требований безопасности, платформы и приложений вашей компании.

Ниже приведены некоторые факторы, которые следует учитывать при выборе технологии уменьшения пароля Майкрософт.

||**Настройка Windows Hello для бизнеса**|**Вход без пароля с помощью приложения Microsoft Authenticator**|**Ключи безопасности FIDO2**|
|:-|:-|:-|:-|
|**Предварительные требования**| Windows 10, версия 1809 или более поздняя<br>Azure Active Directory| Приложение Microsoft Authenticator<br>Phone (устройства iOS и Android с Android 6,0 или более поздней версии).|Windows 10, версия 1809 или более поздняя<br>Azure Active Directory|
|**Режим**|Платформа|Программное обеспечение|Оборудование|
|**Системы и устройства**|ПК со встроенным доверенный платформенный модуль (TPM) (TPM)<br>Распознавание ПИН-кодов и биометрические изменения |Закрепление и распознавание биометрических изображений на телефоне|FIDO2 устройства безопасности, совместимые с Майкрософт|
|**Взаимодействие с пользователем**|Вход с использованием ПИН-кода или биометрического распознавания (лица, IRI или отпечатка пальца) с устройствами Windows.<br>Проверка подлинности Windows Hello привязана к устройству; для доступа к корпоративным ресурсам пользователю требуются как устройство, так и компонент входа, например, ПИН-код или биометрический фактор.|Войдите на мобильный телефон, используя сканер отпечатков пальцев, распознавание лиц или IRI или ПИН-код.<br>Пользователи входят в рабочую или личную учетную запись с компьютера или с мобильного телефона.|Вход с помощью устройства безопасности FIDO2 (биометрии, ПИН-кода и NFC)<br>Пользователь может получить доступ к устройству на основе управления Организацией и пройти проверку подлинности на основе ПИН-кода, биометрии с использованием таких устройств, как ключи безопасности USB и SmartCards, ключи или переносные устройства с поддержкой NFC.|
|**Включенные сценарии**| Удобство использования пароля с помощью устройства Windows.<br>Применимо к выделенному рабочему компьютеру с возможностью единого входа на устройство и приложения.|Решение без пароля в любом месте с помощью мобильного телефона.<br>Применяется для доступа к рабочим или личным приложениям в Интернете с любого устройства.|Удобство работы с паролями для сотрудников, использующих биометрию, ПИН-код и NFC.<br>Применимо к общим компьютерам и в том случае, когда мобильный телефон не является приемлемым вариантом (например, для персонала службы поддержки, в общедоступном киоске или в группе больницы).|

Используйте следующую таблицу, чтобы выбрать метод, который будет поддерживать ваши требования и пользователей.

|Описания|Сценарий|Среда|Технология, не защищенная паролем|
|:-|:-|:-|:-|
|**Администратор**|Безопасный доступ к устройству для задач управления|Назначенное устройство Windows 10|Ключ безопасности Windows Hello для бизнеса или FIDO2|
|**Администратор**|Задачи управления на устройствах, отличных от Windows| Мобильное или не Windows-устройство|Вход без пароля с помощью приложения Microsoft Authenticator|
|**Информационный работник**|Продуктивность работы|Назначенное устройство Windows 10|Ключ безопасности Windows Hello для бизнеса или FIDO2|
|**Информационный работник**|Продуктивность работы| Мобильное или не Windows-устройство|Вход без пароля с помощью приложения Microsoft Authenticator|
|**Оказался Worker**|Киоски в фабрике, растения, розничной торговле или вводе данных|Общие устройства с Windows 10|Ключи безопасности FIDO2|

## <a name="getting-started"></a>Начало работы

Проверка подлинности с паролем — это волна в будущем и путь к более безопасной среде. Рекомендуется, чтобы организации начали планировать это изменение и сократить их зависимости от паролей. Чтобы приступить к работе, учитывайте следующие цели.

* Разрешить пользователям MFA + Microsoft Authenticator приложение + условный доступ.

* Развертывание политики защиты паролей Azure AD и обновлений.

* Разрешить пользователям SSPR с Объединенной регистрацией.

* Развертывание Microsoft Authenticator приложения для мобильных устройств.

* Развертывание Windows Hello для бизнеса (1909: Оставайтесь в актуальном виде).

* Разверните устройства FIDO2 для пользователей, которые не могут использовать телефоны.

* По возможности отключите проверку подлинности на основе пароля.

Для достижения этих целей рекомендуется использовать следующий подход:

1. Включите Azure Active Directory, чтобы воспользоваться всеми преимуществами таких функций, как Azure MFA и условный доступ.

2. Включите многофакторную проверку подлинности, чтобы обеспечить дополнительную защиту.

3. Включите самостоятельный сброс пароля в событиях, когда пользователи должны вернуться к использованию пароля.

4. Разверните Microsoft Authenticator вход с телефона для добавления мобильности.

5. Развертывание Windows Hello для бизнеса на всех устройствах Windows 10.

6. Подготовка к использованию ключей безопасности FIDO2.

> [!NOTE]
> Дополнительные сведения о требованиях к лицензированию для методов, отменяющих пароль, см. на [странице лицензирования](https://azure.microsoft.com/pricing/details/active-directory/) Azure Active Directory.

## <a name="conclusion"></a>Заключение

За последние несколько лет корпорация Майкрософт пропустила свои обязательства по включению мира без паролей. В Windows 10 Корпорация Майкрософт представила Windows Hello для бизнеса, надежный защищенный от оборудования Многофакторные учетные данные, обеспечивающие единый вход в Azure Active Directory и Active Directory. Подобно технологии Windows Hello для бизнеса, Microsoft Authenticator приложение использует проверку подлинности на основе ключей, чтобы включить учетные данные пользователя, привязанные к мобильному устройству и использующие биометрические метрики или ПИН-код. Теперь FIDO2 ключи безопасности, позволяющие вам передавать учетные данные вам и входить в Azure AD, выбрав ключ безопасности в качестве поставщика учетных данных на экране блокировки Windows 10. Все три из этих решений без пароля снижают риск фишинга, мошенничества и воспроизведения паролей, а также предоставляют пользователям надежный и удобный способ входа и доступа к данным из любого места.

Внедрение современных технологий многофакторной проверки подлинности, таких как биометрию и шифрование с открытым ключом, в общедоступных устройствах является одним из самых важных этапов, которые могут существенно снизить риск идентификации компании. Безпарольный подход является долгосрочным методом безопасной проверки подлинности и продолжает развиваться. Учитывая новые требования, организации могут подготовиться к работе, создав план перехода к технологиям, не защищенным паролем.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о том [, что такое пароль?](../../active-directory/authentication/concept-authentication-passwordless.md)
* [Включение безпарольной поддержки в Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
