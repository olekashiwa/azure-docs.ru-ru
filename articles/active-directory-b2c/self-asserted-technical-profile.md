---
title: Определение самостоятельно утвержденного технического профиля в настраиваемой политике
titleSuffix: Azure AD B2C
description: Определение самоподтвержденного технического профиля в настраиваемой политике в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a16fb1184de5b545b3ef527b1a66ffb7b68d1ef4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197924"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Определение самоподтвержденного технического профиля в настраиваемой политике Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Все взаимодействия в Azure Active Directory B2C (Azure AD B2C), где пользователь должен предоставить входные данные, — это самостоятельно утвержденные технические профили. Например, страница регистрации, страница входа, страница сброса пароля.

## <a name="protocol"></a>Протокол

Атрибуту **Name** элемента **Protocol** необходимо присвоить значение `Proprietary`. Атрибут **handler** должен содержать полное имя сборки обработчика протокола, которое используется Azure AD B2C, для самоподтверждения: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

В следующем примере показан самоподтвержденный технический профиль для регистрации по электронной почте:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Входящие утверждения

В самостоятельно утвержденном техническом профиле можно использовать элементы **inputclaim** и **инпутклаимстрансформатионс** для предварительного заполнения значений утверждений, которые отображаются на странице с самоподтверждением (отображение утверждений). Например, в политике изменения профиля путь взаимодействия пользователя сначала считывает профиль пользователя из службы каталогов Azure AD B2C, а затем самоподтвержденный технический профиль задает входящие утверждения с помощью пользовательских данных, хранящихся в профиле пользователя. Эти утверждения собираются из профиля пользователя, а затем представляются пользователю, который после этого может изменить существующие данные.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Отображение утверждений

Функция "Показать утверждения" в настоящее время доступна в **предварительной версии**.

Элемент **дисплайклаимс** содержит список заявок, которые должны быть представлены на экране для сбора данных от пользователя. Чтобы предварительно заполнить значения исходящих утверждений, используйте входные утверждения, которые были описаны ранее. Элемент также может содержать значение по умолчанию.

Порядок утверждений в **дисплайклаимс** указывает порядок, в котором Azure AD B2C отображает утверждения на экране. Чтобы заставить пользователя предоставить значение для конкретного утверждения, задайте для атрибута **Required** элемента **дисплайклаим** `true`.

Элементу **"множество" в** коллекции **дисплайклаимс** необходимо задать для элемента **усеринпуттипе** любой пользовательский тип ввода, поддерживаемый Azure AD B2C. Например, `TextBox` или `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Добавление ссылки на элемент типа

В коллекции "Отображение утверждений" можно включить ссылку на созданный элемент типа ( [DisplayControl](display-controls.md) ). Элемент управления "Отображение" — это элемент пользовательского интерфейса, который имеет специальные функции и взаимодействует с серверной службой Azure AD B2C. Она позволяет пользователю выполнять действия на странице, которая вызывает технический профиль проверки на серверной части. Например, проверьте адрес электронной почты, номер телефона или номер клиента по программе лояльности.

В следующем примере `TechnicalProfile` демонстрируется использование отображаемых утверждений с элементами управления отображением.

* Первое утверждение экрана создает ссылку на элемент управления отображением `emailVerificationControl`, который собирает и проверяет адрес электронной почты.
* Пятый запрос на отображение создает ссылку на элемент управления отображением `phoneVerificationControl`, который собирает и проверяет номер телефона.
* Другие утверждения на отображение ClaimTypesся для сбора от пользователя.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Как уже упоминалось, утверждение на отображение со ссылкой на элемент управления отображением может выполнять собственную проверку, например проверку адреса электронной почты. Кроме того, самоподтвержденная страница поддерживает использование технического профиля проверки для проверки всей страницы, включая ввод данных пользователем (типы утверждений или элементы управления отображением), перед переходом к следующему этапу оркестрации.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Тщательное объединение использования отображаемых утверждений и запросов на выводе

Если указать один или несколько элементов **дисплайклаим** в самостоятельно утвержденном техническом профиле, необходимо использовать дисплайклаим для *каждого* утверждения, которое должно отображаться на экране и получать от пользователя. В самостоятельно утвержденном техническом профиле, содержащем хотя бы одно утверждение, не отображаются выходные утверждения.

Рассмотрим следующий пример, в котором утверждение `age` определено как **выходное** утверждение в базовой политике. Перед добавлением любых утверждений отображения в самоподтвержденный технический профиль `age` на экране отображается утверждение для сбора данных от пользователя:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Если конечная политика, которая наследует этот базовый объект, в дальнейшем указывает `officeNumber` как утверждение на **Отображение** :

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

Утверждение `age` в базовой политике больше не отображается на экране для пользователя — он фактически скрыт. Чтобы отобразить утверждение `age` и получить значение Age от пользователя, необходимо добавить `age` **дисплайклаим**.

## <a name="output-claims"></a>Исходящие утверждения

Элемент **OutputClaims** содержит список заявок, возвращаемых к следующему этапу оркестрации. Атрибут **DefaultValue** вступает в силу только в том случае, если утверждение никогда не было задано. Если он был задан в предыдущем шаге оркестрации, значение по умолчанию не вступит в силу, даже если пользователь оставляет значение пустым. Чтобы принудительно использовать значение по умолчанию, задайте атрибуту **AlwaysUseDefaultValue** значение `true`.

> [!NOTE]
> В предыдущих версиях инфраструктуры процедур идентификации (инфраструктура процедур идентификации) выходные утверждения использовались для получения данных от пользователя. Чтобы собирать данные от пользователя, используйте вместо этого коллекцию **дисплайклаимс** .

Элемент **OutputClaimsTransformations** может содержать коллекцию элементов **OutputClaimsTransformation**, которые используются для изменения исходящих утверждений или создания новых.

### <a name="when-you-should-use-output-claims"></a>Когда следует использовать исходящие утверждения

В самостоятельно утвержденном техническом профиле коллекция утверждений OUTPUT возвращает утверждения на следующий этап оркестрации.

Исходящие утверждения следует использовать в следующих случаях:

- Утверждения выводятся **преобразованием "выходные утверждения**".
- **Установка значения по умолчанию в выходном утверждении** без сбора данных от пользователя или возврата данных из технического профиля проверки. Самоподтвержденный технический профиль `LocalAccountSignUpWithLogonEmail` задает утверждению **executed-SelfAsserted-Input** значение `true`.
- **Технический профиль проверки возвращает исходящие утверждения**. Технический профиль может вызывать технический профиль проверки, который возвращает некоторые утверждения. Вы можете перенаправить утверждения и вернуть их на дальнейшие шаги оркестрации на пути взаимодействия пользователя. Например, при входе в систему с помощью локальной учетной записи самоподтвержденный технический профиль с именем `SelfAsserted-LocalAccountSignin-Email` вызывает технический профиль проверки с именем `login-NonInteractive`. Этот технический профиль проверяет учетные данные пользователя и возвращает профиль пользователя. Например, userPrincipalName, displayName, givenName и surName.
- **Элемент управления "Отображение" возвращает выходные утверждения** . ваш технический профиль может иметь ссылку на [элемент управления "Отображение](display-controls.md)". Элемент управления отображением возвращает некоторые утверждения, например проверенный адрес электронной почты. Вы можете перенаправить утверждения и вернуть их на дальнейшие шаги оркестрации на пути взаимодействия пользователя. Функция элемента управления отображением в настоящее время доступна в **предварительной версии**.

В следующем примере демонстрируется использование самостоятельно утвержденного технического профиля, который использует как отображаемые утверждения, так и выходные утверждения.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Сохранение утверждений

Если элемент **PersistedClaims**отсутствует, самоподтвержденный технический профиль не сохраняет данные в Azure AD B2C. Вместо этого выполняется вызов к техническому профилю проверки, который отвечает за сохранение данных. Например, политика регистрации использует самоподтвержденный технический профиль `LocalAccountSignUpWithLogonEmail` для сбора нового пользовательского профиля. Технический профиль `LocalAccountSignUpWithLogonEmail` вызывает технический профиль проверки для создания учетной записи в Azure AD B2C.

## <a name="validation-technical-profiles"></a>Технические профили проверки

Технический профиль проверки используется для проверки некоторых или всех исходящих утверждений эталонного технического профиля. Входящие утверждения технического профиля проверки должны появиться в исходящих утверждениях самоподтвержденного технического профиля. Технический профиль проверки проверяет входные данные пользователя и возвращает ошибку пользователю.

Техническим профилем проверки может быть любой технический профиль в политике, например технические профили [Azure Active Directory](active-directory-technical-profile.md) или [REST API](restful-technical-profile.md). В предыдущем примере технический профиль `LocalAccountSignUpWithLogonEmail` проверяет отсутствие имени signinName в каталоге. Если оно отсутствует, технический профиль проверки создает локальную учетную запись и возвращает objectId, authenticationSource и newUser. Технический профиль `SelfAsserted-LocalAccountSignin-Email` вызывает технический профиль проверки `login-NonInteractive` для проверки учетных данных пользователя.

Можно также вызывать технический профиль REST API с бизнес-логикой, перезаписывать входящие утверждения или дополнять пользовательские данные за счет дальнейшей интеграции с бизнес-приложением корпоративного уровня. Дополнительные сведения см. в статье [о техническом профиле проверки](validation-technical-profile.md).

## <a name="metadata"></a>Метаданные

| Атрибут | Обязательно | Описание |
| --------- | -------- | ----------- |
| setting.operatingMode | Нет | Для страницы входа в систему это свойство управляет поведением поля имени пользователя, таким как проверка входных данных и вывод сообщений об ошибках. Ожидаемые значения: `Username` или `Email`. |
| алловженератионофклаимсвиснуллвалуес| Нет| Разрешить создание утверждения со значением NULL. Например, в случае, если пользователь не выберет флажок.|
| ContentDefinitionReferenceId | Да | Идентификатор [определения содержимого](contentdefinitions.md), связанного с этим техническим профилем. |
| EnforceEmailVerification | Нет | При регистрации или изменении профиля применяет проверку по электронной почте. Возможные значения: `true` (по умолчанию) или `false`. |
| setting.retryLimit | Нет | Определяет, сколько раз пользователь может попытаться предоставить данные, что проверяется на соответствие техническому профилю проверки. Например, пользователь пытается зарегистрироваться с помощью учетной записи, которая уже существует, и продолжает попытки до достижения предела.
| SignUpTarget | Нет | Идентификатор обмена целевого объекта регистрации. Когда пользователь нажимает кнопку "Регистрация", Azure AD B2C выполняет указанный идентификатор обмена. |
| setting.showCancelButton | Нет | Отображает кнопку "Отмена". Возможные значения: `true` (по умолчанию) или `false`. |
| setting.showContinueButton | Нет | Отображает кнопку "Продолжить". Возможные значения: `true` (по умолчанию) или `false`. |
| setting.showSignupLink | Нет | Отображает кнопку "Регистрация". Возможные значения: `true` (по умолчанию) или `false`. |
| задание. Форготпассвордлинклокатион| Нет| Отображает ссылку Забыли пароль. Возможные значения: `AfterInput` (по умолчанию). ссылка отображается в нижней части страницы или `None` удаляет ссылку "Забыли пароль".| 
| инклудеклаимресолвингинклаимшандлинг  | Нет | Для входных и выходных утверждений указывает, включено ли [разрешение утверждений](claim-resolver-overview.md) в технический профиль. Возможные значения: `true`или `false` (по умолчанию). Если вы хотите использовать сопоставитель утверждений в техническом профиле, задайте для этого параметра значение `true`. |

## <a name="cryptographic-keys"></a>Криптографические ключи

Элемент **CryptographicKeys** не используется.
