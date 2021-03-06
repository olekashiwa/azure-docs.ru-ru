---
title: Приложения, & субъекты-службы в Azure AD | Службы
titleSuffix: Microsoft identity platform
description: Узнайте о связи между объектами приложений и субъектами-службами в Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 19085346fb5797245c9f71911f8178df0a1b742a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698430"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Объекты приложения и субъекта-службы в Azure Active Directory

Иногда значение термина "приложение" может быть непонятно при использовании в контексте Azure Active Directory (Azure AD). В этой статье разъясняются концептуальные и практические аспекты интеграции приложения Azure AD, а также проиллюстрирована регистрация [мультитенантного приложения](developer-glossary.md#multi-tenant-application) и предоставление соответствующих разрешений.

## <a name="overview"></a>Обзор

Приложение, интегрированное с Azure AD, это более широкое понятие, чем просто компонент программного обеспечения. "Приложение" — это общий термин, который включает в себя не только программное обеспечение, но и процесс регистрации приложения в Azure AD и роль при обмене данными во время проверки подлинности и авторизации в среде выполнения.

По определению приложение может функционировать в этих ролях:

- роль [клиента](developer-glossary.md#client-application) (потребление ресурсов);
- роль [сервера ресурсов](developer-glossary.md#resource-server) (предоставление API для клиентов) ;
- обе роли (клиента и сервера ресурсов).

[Процесс предоставления авторизации OAuth 2.0](developer-glossary.md#authorization-grant) определяет протокол общения, который позволяет клиенту или ресурсу получать доступ или защиту данных ресурса соответственно.

В следующих разделах вы увидите, как модель приложения Azure AD представляет приложение во время разработки и во время выполнения.

## <a name="application-registration"></a>Регистрация приложения

При регистрации приложения Azure AD в [портал Azure][AZURE-Portal]в клиенте Azure AD создаются два объекта:

- объект приложения;
- объект субъекта-службы.

### <a name="application-object"></a>Объект приложения

Объект приложения представляет определение вашего приложения Azure AD. Единственный экземпляр объекта приложения размещается в главном клиенте Azure AD приложения (то есть в клиенте, в котором зарегистрировано приложение). [Сущность приложения][MS-Graph-App-Entity] Microsoft Graph определяет схему для свойств объекта приложения.

### <a name="service-principal-object"></a>Объект субъекта-службы

Чтобы получить доступ к ресурсам, которые защищены с помощью клиента Azure AD, сущность, которой требуется доступ, должна быть представлена субъектом безопасности. Это применимо и к пользователям (субъект-пользователь), и к приложениям (субъект-служба).

Субъект безопасности определяет политику доступа и разрешения для пользователя или приложения в клиенте Azure AD. Это обеспечивает базовые функции, включая аутентификацию пользователя или приложения во время входа, а также авторизацию во время получения доступа к ресурсам.

Когда приложение получает разрешение на доступ к ресурсам в клиенте (при регистрации или [предоставлении согласия](developer-glossary.md#consent)), создается объект субъекта службы. [Сущность Microsoft Graph ServicePrincipal][MS-Graph-Sp-Entity] определяет схему для свойств объекта субъекта-службы.

### <a name="application-and-service-principal-relationship"></a>Отношение приложения и субъекта-службы

В некотором роде объект приложения является *глобальным* представлением вашего приложения (для использования во всех клиентах), а субъект-служба — *локальным* (для использования в определенных клиентах).

Объект приложения служит шаблоном, который *определяет* общие свойства и свойства по умолчанию, используемые для создания соответствующих объектов субъекта-службы. Между объектом приложения и самим приложением устанавливается отношение "один к одному", а между объектом приложения и соответствующими субъектами-службами — отношение "один ко многим".

Субъект-службу необходимо создать в каждом клиенте, где будет использоваться приложение, чтобы установить поставщика удостоверений для входа в систему и доступа к ресурсам, защищенным с помощью клиента. Приложение с одним клиентом будет иметь только один субъект-службу (в своем домашнем клиенте). Создание субъекта-службы и предоставление разрешения на его использование выполняется во время регистрации приложения. Для мультитенантного веб-приложения или API также будет создан субъект-служба в каждом клиенте, пользователь которого даст согласие на его использование.

> [!NOTE]
> Любые изменения объекта приложения также отражаются в объекте субъекта-службы, размещенном в главном клиенте приложения (то есть в клиенте, в котором зарегистрировано приложение). При использовании мультитенантных приложений изменения объекта приложения не отражаются в объектах субъекта-службы клиента-потребителя до тех пор, пока через [панель доступа приложения](https://myapps.microsoft.com) доступ не будет удален и предоставлен снова.
>
> Обратите также внимание, что собственные приложения по умолчанию регистрируются как мультитенантные.

## <a name="example"></a>Пример

На следующей схеме показана связь между объектом приложения и соответствующими объектами субъекта-службы в контексте образца мультитенантного приложения под названием **Приложение по управлению персоналом**. В этом примере сценария используются три клиента Azure AD:

- **Adatum** — клиент, который использует компания, разработавшая **приложение по управлению персоналом**;
- **Contoso** — клиент, который использует компания Contoso, являющаяся объектом-получателем **приложения по управлению персоналом**;
- **Fabrikam** — клиент, который использует компания Fabrikam, также потребляющая **приложение по управлению персоналом**.

![Отношение между объектом приложения и объектом субъекта-службы](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Условия в этом примере сценария.

| Шаг | Description |
|------|-------------|
| 1    | Это процесс создания объектов приложения и субъекта-службы в главном клиенте приложения. |
| 2    | При согласии администраторов компании Contoso и Fabrikam в клиенте Azure AD компании создается объект субъекта-службы, и ему назначаются разрешения, предоставленные администратором. Обратите внимание, что приложение по управлению персоналом можно создать или настроить для отдельных пользователей. |
| 3    | Каждый из клиентов-потребителей приложения по управлению персоналом (Contoso и Fabrikam) имеет собственный объект субъекта-службы. И каждый из них представляет использование экземпляра приложения во время выполнения. Это использование зависит от разрешений, предоставленных соответствующим администратором. |

## <a name="next-steps"></a>Дальнейшие действия

- Можно использовать [проводник Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) для запроса объектов приложения и субъекта-службы.
- Доступ к объекту приложения приложения можно получить с помощью Microsoft Graph API, редактора манифеста приложения [портал Azure][AZURE-Portal] или [командлетов Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), представленных в [сущности приложения][MS-Graph-App-Entity]OData.
- Доступ к объекту субъекта-службы приложения можно получить с помощью Microsoft Graph API или [командлетов Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), представленных в [сущности ServicePrincipal][MS-Graph-Sp-Entity]OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
