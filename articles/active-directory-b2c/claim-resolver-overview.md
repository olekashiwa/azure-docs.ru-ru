---
title: Арбитры утверждений в пользовательских политиках
titleSuffix: Azure AD B2C
description: Узнайте, как использовать арбитры утверждений в пользовательской политике в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1734b063530f9e8a8f0429111c4c39d628bfad4e
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251776"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Арбитры утверждений в пользовательских политиках Azure Active Directory B2C

Арбитры утверждений в Azure Active Directory B2C (Azure AD B2C) [пользовательские политики](custom-policy-overview.md) предоставляют контекстные сведения о запросе авторизации, такие как имя политики, идентификатор корреляции запроса, язык пользовательского интерфейса и многое другое.

Чтобы использовать арбитр утверждений во входящем или исходящим утверждении, определите строку **ClaimType** в элементе [ClaimsSchema](claimsschema.md), а затем установите **DefaultValue** в арбитр утверждений в элементе входящего или исходящего утверждения. Azure AD B2C считывает значение арбитра утверждений и использует значение в техническом профиле.

В следующем примере тип претензии с именем `correlationId` определяется с помощью **DataType** из `string`.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

В техническом профиле сопоставьте арбитры утверждений с типом утверждений. Azure AD B2C заполняет значение арбитра утверждений `{Context:CorrelationId}` в утверждение `correlationId` и отправляет утверждение в технический профиль.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Типы арбитров утверждений

В следующих разделах перечислены доступные арбитры утверждений.

### <a name="culture"></a>Язык и региональные параметры

| Утверждение | Описание | Пример |
| ----- | ----------- | --------|
| {Culture:LanguageName} | Двухбуквенный код ISO для языка. | en |
| {Culture:LCID}   | Код языка (локаль). | 1033 |
| {Culture:RegionName} | Двухбуквенный код ISO для региона. | US |
| {Culture:RFC5646} | Код языка RFC5646. | ru-RU |

### <a name="policy"></a>Политика

| Утверждение | Описание | Пример |
| ----- | ----------- | --------|
| {Policy:PolicyId} | Имя политики проверяющей стороны. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | Идентификатор клиента для политики проверяющей стороны. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | Идентификатор объекта клиента для политики проверяющей стороны. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | Идентификатор клиента инфраструктуры доверия. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Утверждение | Описание | Пример |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Параметр `acr_values` строки запроса. | Н/Д |
| {OIDC:ClientId} |Параметр `client_id` строки запроса. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Параметр `domain_hint` строки запроса. | facebook.com |
| {OIDC:LoginHint} |  Параметр `login_hint` строки запроса. | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`. | Н/Д |
| {OIDC:Nonce} |Параметр `Nonce` строки запроса. | defaultNonce |
| {OIDC:Prompt} | Параметр `prompt` строки запроса. | login |
| {OIDC:Resource} |Параметр `resource` строки запроса. | Н/Д |
| {OIDC:scope} |Параметр `scope` строки запроса. | OpenId |

### <a name="context"></a>Контекст

| Утверждение | Описание | Пример |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Версия инфраструктури процедур идентификации (номер сборки).  | 1.0.507.0 |
| {Context:CorrelationId} | Идентификатор корреляции.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Дата и время в формате UTC.  | 10/10/2018 12:00:00 |
| {Context:DeploymentMode} |Режим развертывания политики.  | Рабочие |
| {Context:IPAddress} | IP-адрес пользователя. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Не протокольные параметры

Любое имя параметра, включенное в запрос OIDC или OAuth2, можно сопоставить с утверждением в пути взаимодействия пользователя. Например, запрос из приложения может включать в себя параметр строки запроса с именем `app_session`, `loyalty_number` или любую пользовательскую строку запроса.

| Утверждение | Описание | Пример |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Параметр строки запроса. | Гавайи |
| {OAUTH-KV:app_session} | Параметр строки запроса. | A3C5R |
| {OAUTH-KV:loyalty_number} | Параметр строки запроса. | 1234 |
| {OAUTH-KV:any custom query string} | Параметр строки запроса. | Н/Д |

### <a name="oauth2"></a>OAuth2

| Утверждение | Описание | Пример |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Маркер доступа. | Н/Д |

## <a name="using-claim-resolvers"></a>Использование арбитров утверждений 

Вы можете использовать арбитры утверждений со следующими элементами: 

| Элемент | Элемент | Настройки |
| ----- | ----------------------- | --------|
|Технический профиль Application Insights |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) технический профиль| `InputClaim`, `OutputClaim`| 1, 2|
|Технический профиль [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Технический профиль [OpenID Connect Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Технический профиль [преобразования утверждений](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Технический профиль [поставщика RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Технический профиль [SAML2](saml-technical-profile.md)| `OutputClaim`| 1, 2|
|[Самостоятельно утвержденный](self-asserted-technical-profile.md) технический профиль| `InputClaim`, `OutputClaim`| 1, 2|
|[контентдефинитион](contentdefinitions.md)| `LoadUri`| |
|[контентдефинитионпараметерс](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Технический профиль [релингпарти](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Параметры: 
1. Для метаданных `IncludeClaimResolvingInClaimsHandling` должно быть задано значение `true`.
1. `AlwaysUseDefaultValue` атрибута входящих или исходящих утверждений должен быть установлен в значение `true`.

## <a name="claim-resolvers-samples"></a>Примеры арбитров утверждений

### <a name="restful-technical-profile"></a>Технический профиль REST

В техническом профиле [REST](restful-technical-profile.md) вам может потребоваться отправить язык пользователя, имя политики, область резервирования и идентификатор клиента. На основе этих утверждений REST API может запускать пользовательскую бизнес-логику и при необходимости вызвать локализованное сообщение об ошибке.

В следующем примере показан технический профиль RESTFUL с использованием этого сценария:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Прямой вход в систему

С помощью арбитров утверждений вы можете настроить предварительное заполнение имени для входа или прямой вход в поставщик удостоверений в определенных социальных сетях, например Facebook или LinkedIn, или учетной записи Майкрософт. Дополнительные сведения см. в статье [Настройка прямого входа в систему с помощью Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Настройка динамического пользовательского интерфейса

Azure AD B2C позволяет передавать параметры строки запроса конечным точкам определения содержимого HTML для динамического отображения содержимого страницы. Например, это позволяет изменять фоновое изображение на Azure AD B2C странице регистрации или входа в систему на основе настраиваемого параметра, передаваемого из веб-приложения или с помощью мобильных приложений. Дополнительные сведения см. в статье [Azure Active Directory B2C: настройка пользовательского интерфейса с динамическим содержимым, используя пользовательские политики](custom-policy-ui-customization-dynamic.md). Вы также можете локализовать свою HTML-страницу на основе параметра языка или изменить содержимое на основе идентификатора клиента.

Следующий пример передает параметр строки запроса с именем **campaignId** со значением `hawaii`, кодом **языка** `en-US`и **приложением** , представляющим идентификатор клиента:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

В результате Azure AD B2C отправляет приведенные выше параметры на страницу содержимого HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Определение содержимого

В `LoadUri`[контентдефинитион](contentdefinitions.md) можно отправить арбитры утверждений для извлечения содержимого из разных мест в зависимости от используемых параметров. 

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Технический профиль Application Insights

С помощью Azure Application Insights и арбитров утверждений можно получить информацию о поведении пользователя. В техническом профиле Application Insights вы отправляете входящие утверждения, которые сохраняются в Azure Application Insights. Дополнительные сведения см. в статье [Отслеживание поведения пользователей в путях взаимодействия Azure AD B2C с использованием Application Insights](analytics-with-application-insights.md). Следующий пример отправляет идентификатор политики, идентификатор корреляции, язык и идентификатор клиента в Azure Application Insights.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Политика проверяющей стороны

В техническом профиле политики [проверяющей](relyingparty.md) стороны может ПОТРЕБОВАТЬСЯ отправить идентификатор клиента или идентификатор корреляции в приложение проверяющей стороны в JWT. 

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
