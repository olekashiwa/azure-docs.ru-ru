---
title: Методы проверки подлинности — Azure Active Directory
description: Методы проверки подлинности, доступные в Azure AD для MFA и SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee0dd0cd83ab27dd728a7572b6fcd69c40bb1b00
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848754"
---
# <a name="what-are-authentication-methods"></a>Какие методы проверки подлинности доступны?

В качестве администратора, выбрав методы проверки подлинности для многофакторной идентификации Azure и самостоятельного сброса пароля (SSPR), рекомендуется требовать от пользователей регистрировать несколько методов проверки подлинности. Если метод проверки подлинности недоступен для пользователя, он может выбрать проверку подлинности с помощью другого метода.

Администраторы могут определить в политике, какие методы проверки подлинности доступны для пользователей SSPR и MFA. Некоторые методы проверки подлинности могут быть недоступны для всех функций. Дополнительные сведения о настройке политик см. в статьях [как успешно развернуть самостоятельный сброс пароля](howto-sspr-deployment.md) и [планирование многофакторной идентификации Azure на основе облачных](howto-mfa-getstarted.md) служб.

Корпорация Майкрософт настоятельно рекомендует администраторам предоставить пользователям на выбор больше минимального необходимого количества методов проверки подлинности на случай, если они у ни не будет доступа к одному из них.

|Метод проверки подлинности|Использование|
| --- | --- |
| Пароль | MFA и SSPR |
| Контрольные вопросы | Только SSPR |
| Адрес электронной почты | Только SSPR |
| Приложение Microsoft Authenticator | MFA и SSPR |
| Токен оборудования OATH | Общедоступная предварительная версия MFA и SSPR |
| SMS | MFA и SSPR |
| Голосовой звонок | MFA и SSPR |
| Пароли приложений | Только MFA в некоторых случаях |

![Методы проверки подлинности, используемые на экране входа](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Токены оборудования OATH для MFA и SSPR — это общедоступная Предварительная версия функций Azure Active Directory. См. дополнительные сведения о [дополнительных условиях использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="password"></a>Пароль

Пароль Azure AD считается методом проверки подлинности. Это метод, который **невозможно отключить**.

## <a name="security-questions"></a>Контрольные вопросы

Контрольные вопросы доступны **только при самостоятельном сбросе пароля в Azure AD** для учетных записей без прав администратора.

Мы рекомендуем использовать контрольные вопросы вместе с другим методом. Контрольные вопросы могут быть менее безопасными, чем другие методы, так как некоторые пользователи могут знать ответы на вопросы других пользователей.

> [!NOTE]
> Контрольные вопросы надежно хранятся в объекте пользователя в каталоге. Ответы на них пользователь может задать только в процессе регистрации. Администратор не может прочитать или изменить вопросы или ответы пользователя.
>

### <a name="predefined-questions"></a>Стандартные вопросы

* В каком городе вы познакомились с первой супругой или первым супругом?
* В каком городе познакомились ваши родители?
* В каком городе живет ваш ближайший родственник?
* В каком городе родился ваш отец?
* В каком городе была ваша первая работа?
* В каком городе родилась ваша мать?
* В каком городе вы провели Новый год в 2000 году?
* Какая фамилия у вашего любимого учителя в старшей школе?
* Как называлось учебное заведение, в которое вы поступили, но не стали учиться?
* В каком месте была ваша первая свадебная церемония?
* Какое отчество у вашего отца?
* Какое ваше любимое блюдо?
* Какие имя и фамилия у вашей бабушки по материнской линии?
* Какое отчество у вашей матери?
* Какой месяц и год рождения вашего старшего брата или сестры? (Например, ноябрь 1985 г.)
* Какое отчество у вашего самого старого родственника?
* Какие имя и фамилия у вашего дедушки по отцовской линии?
* Какое отчество у вашего самого младшего родственника?
* Какую школу вы посещали в шестом классе?
* Какие имя и фамилия у вашего лучшего друга детства?
* Какие имя и фамилия у вашей первой любви?
* Какая фамилия у вашего любимого учителя в начальной школе?
* Какая марка и модель вашей первой машины или мотоцикла?
* Как называлась первая школа, в которую вы ходили?
* Как называлось медицинское учреждение, в котором вы родились?
* Как называлась первая улица, где вы жили в детстве?
* Как звали героя вашего детства?
* Как звали вашу любимую плюшевую игрушку?
* Как звали вашего первого питомца?
* Какое прозвище было у вас в детстве?
* Какой вид спорта вы любили в старших классах?
* Какова ваша первая работа?
* Какие последние 4 цифры вашего номера телефона в детстве?
* Кем вы хотели стать в детстве?
* Какую самую известную личность вы встречали?

Все стандартные контрольные вопросы переведены и локализованы на все языки, поддерживаемые Office 365. Язык вопросов зависит от языкового стандарта браузера пользователя.

### <a name="custom-security-questions"></a>Пользовательские контрольные вопросы

Пользовательские контрольные вопросы не локализованы. Все пользовательские вопросы отображаются на том языке, на котором вы ввели их в интерфейсе пользователя с правами администратора, даже если язык браузера отличается. Если требуются переведенные вопросы, используйте стандартные вопросы.

Максимальная длина пользовательского контрольного вопроса составляет 200 символов.

### <a name="security-question-requirements"></a>Требования к контрольному вопросу

* Минимальное количество знаков в ответе — 3.
* Максимальное количество знаков в ответе — 40.
* Пользователи не могут отвечать на один и тот же вопрос несколько раз.
* Пользователи не могут давать один и тот же ответ на несколько вопросов.
* Для определения вопросов и ответов можно использовать любой набор символов, в том числе символы Юникода.
* Количество определенных вопросов должно быть не меньше числа вопросов, необходимых для регистрации.

## <a name="email-address"></a>Адрес электронной почты

Адрес электронной почты доступен **только при самостоятельном сбросе пароля в Azure AD**.

Корпорация Майкрософт рекомендует использовать учетную запись электронной почты, для доступа к которой не требуется пароль пользователя Azure AD.

## <a name="microsoft-authenticator-app"></a>Приложение Microsoft Authenticator

Приложение Microsoft Authenticator обеспечивает дополнительный уровень безопасности для рабочей или учебной учетной записи Azure AD либо для учетной записи Майкрософт.

Приложение Microsoft Authenticator доступно для [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) и [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> У пользователей не будет возможности зарегистрировать свое мобильное приложение при регистрации для самостоятельного сброса пароля. Вместо этого они смогут зарегистрировать мобильное приложение в службе по адресу [https://aka.ms/mfasetup](https://aka.ms/mfasetup) или в предварительной версии службы регистрации сведений о безопасности по адресу [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Уведомление в мобильном приложении

Приложение Microsoft Authenticator помогает предотвратить несанкционированный доступ к учетным записям и остановить мошеннические транзакции, отправив уведомление на смартфон или планшет. Пользователи могут просмотреть уведомление, и если оно подлинное, щелкнуть "Проверить". В противном случае они могут щелкнуть "Отклонить".

> [!WARNING]
> Если для самостоятельного сброса пароля требуется только один метод, то пользователям будет доступен только сброс с помощью кода проверки, **чтобы обеспечить высший уровень безопасности**.
>
> Если требуются два метода, то пользователи смогут сбросить пароль **ИЛИ** с помощью уведомления, **ИЛИ** с помощью кода проверки в дополнение к любым другим включенным методам.
>

Если включить использование обоих уведомлений посредством мобильного приложения и кода проверки из мобильного приложения, то пользователи, которые регистрируются с помощью системы уведомлений приложения Microsoft Authenticator, смогут использовать уведомление и код для подтверждения своей личности.

> [!NOTE]
> Если в вашей организации сотрудники работают в Китае или **передаются в Китая, уведомление через метод мобильного приложения** на **устройствах Android** не работает в этой стране. Для этих пользователей можно сделать альтернативные методы.

### <a name="verification-code-from-mobile-app"></a>Код проверки от мобильного приложения

Приложение Microsoft Authenticator или приложения сторонних производителей можно использовать в качестве программного токена для создания кода проверки OATH. После ввода имени пользователя и пароля на экране входа требуется ввести код, предоставленный приложением. Код проверки выступает вторым методом проверки подлинности.

> [!WARNING]
> Если для самостоятельного сброса пароля требуется только один метод, то пользователям будет доступен только сброс с помощью кода проверки, **чтобы обеспечить высший уровень безопасности**.
>

У пользователей может быть до пяти маркеров оборудования OATH или приложений для проверки подлинности, таких как приложение Microsoft Authenticator, настроенное для использования в любое время.

## <a name="oath-hardware-tokens-public-preview"></a>Токен оборудования OATH (общедоступная предварительная версия)

OATH является открытым стандартом, который определяет, как генерируются коды одноразовых паролей (OTP). Azure AD будет поддерживать использование токенов OATH-TOTP SHA-1 с 30-секундным или 60-секундным фильтром. Клиенты могут приобрести эти токены у поставщика на свой выбор. Ключи секрета ограничены 128 символами, которые могут быть несовместимы со всеми маркерами. Секретные ключи должны быть закодированы в base32.

![Отправка OATH-токенов в колонку OATH-токенов сервера MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Токены оборудования OATH поддерживаются как часть общедоступной предварительной версии. См. дополнительные сведения о [дополнительных условиях использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

После получения токенов они должны передаваться в формате файлов с разделителями-запятыми (CSV), включая имя участника-пользователя, серийный номер, секретный ключ, интервал времени, изготовитель и модель, как показано в примере ниже.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Убедитесь, что вы включили строку заголовка в CSV-файл, как показано выше.

После правильного форматирования в виде CSV-файла администратор может выполнить вход на портал Azure и перейти на **Azure Active Directory**, **Сервер MFA**, **OATH-токены** и отправить полученный CSV-файл.

В зависимости от размера CSV-файла этот процесс может занять несколько минут. Нажмите кнопку **Обновить**, чтобы получить сведения о текущим состоянии. Если в файле есть какие-либо ошибки, у вас будет возможность загрузить CSV-файл с указанием любых ошибок, которые вы разрешите.

После устранения ошибок администратор может активировать каждый ключ, щелкнув **Активировать** для активации токена, и ввести одноразовый пароль, отображаемый на токене.

У пользователей может быть до пяти маркеров оборудования OATH или приложений для проверки подлинности, таких как приложение Microsoft Authenticator, настроенное для использования в любое время.

## <a name="phone-options"></a>Параметры телефона

### <a name="mobile-phone"></a>Мобильный телефон

Пользователям с мобильными телефонами доступны два метода.

Если пользователи не хотят, чтобы номер их мобильного телефона отображался в каталоге, но по-прежнему хотят использовать его для сброса пароля, администраторы не должны указывать этот номер. Пользователям нужно заполнить атрибут **Телефон для проверки подлинности** на [портале регистрации для сброса пароля](https://aka.ms/ssprsetup). Администраторы смогут просматривать эту информацию в профиле пользователя, но она не будет публиковаться в другом месте.

Для правильной работы номера телефонов должны быть указаны в формате *+код_страны номер_телефона*. Например, +1 4255551234.

> [!NOTE]
> Между кодом страны и номером телефона должен быть пробел.
>
> Функция сброса пароля не поддерживает добавочные номера. Даже добавочные номера в формате +1 4255551234X12345 будут удаляться.

Корпорация Майкрософт не гарантирует единообразного запроса Многофакторной идентификация с помощью SMS или голосового вызова при вводе одного и того же числа. В интересах наших пользователей корпорация Майкрософт может добавить или удалить короткие коды в любое время, так как мы корректируем маршруты для улучшения доставки SMS. Корпорация Майкрософт не поддерживает короткие коды стран и регионов, кроме США и Канады.

#### <a name="text-message"></a>Текстовое сообщение

Текстовое сообщение с кодом проверки отправляется номер мобильного телефона. Этот код проверки следует ввести в интерфейс для входа, чтобы продолжить операцию.

#### <a name="phone-call"></a>Телефонный звонок

По указанному номеру телефона осуществляется автоматический голосовой вызов. Для прохождения аутентификации нужно ответить на вызов и нажать кнопку # на клавиатуре телефона.

> [!IMPORTANT]
> Начиная с марта 2019 параметры телефонного звонка не будут доступны для пользователей MFA и SSPR в бесплатных или пробных клиентах Azure AD. Это изменение не влияет на SMS messages. Телефонный звонок будет по прежнему доступен пользователям в платных клиентах Azure AD. Это изменение касается только бесплатных и пробных клиентов Azure AD.

### <a name="office-phone"></a>Рабочий телефон

По указанному номеру телефона осуществляется автоматический голосовой вызов. Для проверки подлинности нужно ответить на вызов и нажать кнопку # на клавиатуре телефона.

Для правильной работы номера телефонов должны быть указаны в формате *+код_страны номер_телефона*. Например, +1 4255551234.

Атрибутом рабочего телефона управляет администратор.

> [!IMPORTANT]
> Начиная с марта 2019 параметры телефонного звонка не будут доступны для пользователей MFA и SSPR в бесплатных или пробных клиентах Azure AD. Это изменение не влияет на SMS messages. Телефонный звонок будет по прежнему доступен пользователям в платных клиентах Azure AD. Это изменение касается только бесплатных и пробных клиентов Azure AD.

> [!NOTE]
> Между кодом страны и номером телефона должен быть пробел.
>
> Функция сброса пароля не поддерживает добавочные номера. Даже добавочные номера в формате +1 4255551234X12345 будут удаляться.

### <a name="troubleshooting-phone-options"></a>Устранение неполадок с параметрами телефона

Распространенные проблемы, связанные с методами проверки подлинности с использованием номера телефона:

* Заблокированный идентификатор звонящего на одном устройстве
   * Устранение неполадок устройства
* Неправильный номер телефона, неверный код страны, номер домашнего телефона и номер рабочего телефона
   * Устранение неполадок объекта пользователя и настроенных методов проверки подлинности. Убедитесь, что зарегистрированы правильные номера телефонов.
* Введен неправильный ПИН-код
   * Подтвердите, что пользователь использовал правильный ПИН-код, зарегистрированный на сервере Azure MFA.
* Вызов перенаправлен на голосовую почту
   * Убедитесь, что у пользователя включен Телефон, а эта служба доступна в своей области, или используйте другой метод.
* Пользователь заблокирован
   * Администратор разблокирует пользователя в портал Azure.
* На устройстве не создана подписка на SMS
   * Поменяйте методы изменения пользователем или активируйте SMS на устройстве.
* Неисправные поставщики телекоммуникационных услуг (при отсутствии входных данных на телефоне, отсутствие сигналов DTMF, заблокированный идентификатор вызывающего объекта на нескольких устройствах или блокирование SMS на нескольких устройствах)
   * Корпорация Майкрософт использует несколько поставщиков телекоммуникационных услуг для передачи телефонных звонков и сообщений SMS для проверки подлинности. Если вы видите любую из описанных выше проблем, то пользователь попытается использовать метод не менее 5 раз в течение 5 минут и иметь доступ к данным этого пользователя при обращении в службу поддержки Майкрософт.

## <a name="app-passwords"></a>Пароли приложений

Некоторые небраузерные приложения не поддерживают многофакторную проверку подлинности. Если для пользователя включена многофакторная проверка подлинности и он пытается использовать небраузерные приложения, то он не сможет пройти аутентификацию. Пароль приложения позволяет пользователям продолжить аутентификацию.

При принудительном использовании Многофакторной идентификации с применением политики условного доступа, а не параметров многофакторной проверки подлинности для каждого пользователя, создание паролей приложений невозможно. Для приложений, использующих политики условного доступа для управления доступом, пароли приложений не требуются.

Если ваша организация использует федерацию для единого входа Azure AD и вы собираетесь внедрить Azure MFA, то необходимо учитывать следующее:

* Пароль приложения проверяется Azure AD и поэтому обходит федерацию. Федерация используется только при задании пароля приложения. Для федеративных пользователей (использующих единый вход) пароли хранятся в идентификаторе организации. Если пользователь уходит из организации, данная информация должна попадать в идентификатор организации с помощью DirSync. Отключение или удаление учетной записи может занять до трех часов с учетом синхронизации, что задерживает процесс отключения или удаления паролей приложений в Azure AD.
* Параметры контроля доступа локальных клиентов не учитываются при использовании пароля приложения.
* Для паролей приложений не предусмотрена возможность локального ведения журнала и аудита аутентификации.
* Некоторые современные архитектуры при двухфакторной проверке подлинности могут запрашивать как корпоративные учетные данные, так и пароли приложения, в зависимости от того, где выполняется проверка подлинности. Что касается клиентов, выполняющих проверку подлинности в локальной инфраструктуре, можно использовать имя пользователя и пароль, применяющиеся внутри организации. Что касается клиентов, выполняющих проверку подлинности в Azure AD, можно использовать пароль приложения.
* По умолчанию пользователи не могут создавать пароли приложений. Если требуется разрешить пользователям создавать пароли, выберите параметр **Allow users to create app passwords to sign into non-browser applications** (Разрешить пользователям создавать пароли приложений для входа в небраузерные приложения) в разделе "Параметры службы".

## <a name="next-steps"></a>Дальнейшие действия

[Включение самостоятельного сброса пароля для организации](quickstart-sspr.md)

[Включение службы Многофакторной идентификации Azure для организации](howto-mfa-getstarted.md)

[Включение Объединенной регистрации в клиенте](howto-registration-mfa-sspr-combined.md)

[Документация по настройке метода аутентификации пользователей](https://aka.ms/securityinfoguide)
