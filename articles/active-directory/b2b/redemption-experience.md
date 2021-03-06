---
title: Активация приглашения в службе совместной работы B2B — Azure AD
description: Здесь описан процесс активации приглашения в службе совместной работы Azure AD B2B для пользователей, включая принятие условий конфиденциальности.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0a71da025b8b2bb571dc9b00e23bc691ecdd44c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758294"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Активация приглашения службы совместной работы Azure Active Directory B2B

В этой статье описываются способы доступа гостевых пользователей к ресурсам и процесс согласия, который они могут столкнуться. Если вы отправляете приглашению по электронной почте приглашение, оно включает ссылку, которую может активировать гостевой пользователь, чтобы получить доступ к вашему приложению или порталу. Сообщение электронной почты с приглашением — лишь один из способов получения доступа к вашим ресурсам гостями. В качестве альтернативы можно добавить гостей в каталог и предоставить им прямую ссылку на портал или приложение, к которому вы хотите предоставить общий доступ. Независимо от используемого метода, гости проходят через процесс предоставления согласия в первый раз. Этот процесс гарантирует, что ваши гости согласны с условиями конфиденциальности и принимают все настроенные [условия использования](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) .

При добавлении гостевого пользователя в каталог учетная запись гостевого пользователя имеет состояние согласия (отображается в PowerShell), изначально установленное в **пендингакцептанце**. Этот параметр сохраняется до тех пор, пока гостевой компьютер не примет ваше приглашение и соглашается с политикой конфиденциальности и условиями использования. После этого состояние согласия изменится на **принято**, и страницы согласия больше не будут представлены гостевой системе.

## <a name="redemption-through-the-invitation-email"></a>Активация с помощью приглашения по электронной почте

При добавлении гостевого пользователя в каталог с [помощью портал Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)на гостевом компьютере отправляется приглашение по электронной почте. Вы также можете отправить приглашения по электронной почте, когда вы [используете PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) для добавления гостевых пользователей в каталог. Ниже приведено описание взаимодействия с гостевой системой при активации ссылки в сообщении электронной почты.

1. Гостевая программа получает [приглашение по электронной почте](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) , отправленное из **приглашений Майкрософт**.
2. Гостевая ОС выбирает **Начало работы** в сообщении электронной почты.
3. Если у гостя нет учетной записи Azure AD, учетной записи Майкрософт (MSA) или учетной записи электронной почты в федеративной организации, ему будет предложено создать MSA (если только [одноразовый секретный код](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) не включен, что не требует MSA).
4. На гостевой основе вы узнаете, [как описано ниже](#consent-experience-for-the-guest) .

## <a name="redemption-through-a-direct-link"></a>Активация с помощью прямой ссылки

В качестве альтернативы электронному письму с приглашением вы можете предоставить Гостевой веб-каналу прямую ссылку на ваше приложение или портал. Сначала необходимо добавить гостевого пользователя в каталог с помощью [портал Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) или [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Затем можно использовать любой из [настраиваемых способов развертывания приложений для пользователей](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), включая ссылки на прямые входы. Когда гостевая программа использует прямую ссылку, а не электронное письмо с приглашением, она по-прежнему будет работать с согласия пользователя в первый раз.

> [!IMPORTANT]
> Прямая ссылка должна быть специфичной для клиента. Иными словами, он должен включать идентификатор клиента или проверенный домен, чтобы Гостевая учетная запись могла проходить проверку подлинности в клиенте, где расположено общее приложение. Общий URL-адрес, например https://myapps.microsoft.com, не будет работать для гостя, так как он будет перенаправляться на свой домашний клиент для проверки подлинности. Ниже приведены некоторые примеры прямых связей с контекстом клиента.
 > - Панель доступа к приложениям: https://myapps.microsoft.com/?tenantid=&lt; идентификатор клиента&gt; 
 > - Панель доступа к приложениям для проверенного домена: https://myapps.microsoft.com/&lt; проверенный домен&gt;
 > - Портал Azure: https://portal.azure.com/&lt; идентификатор клиента&gt;
 > - Отдельное приложение: см. статью использование [прямого входа](../manage-apps/end-user-experiences.md#direct-sign-on-links) .

В некоторых случаях рекомендуется использовать приглашение по электронной почте по прямой ссылке. Если эти особые случаи важны для вашей организации, рекомендуется пригласить пользователей с помощью методов, которые по-прежнему отправляют приглашение по электронной почте:
 - У пользователя нет учетной записи Azure AD, MSA или учетной записи электронной почты в федеративной организации. Если вы не используете одноразовую функцию секретного кода, гостевой службе необходимо активировать электронное письмо с приглашением, чтобы получить инструкции по созданию MSA.
 - Иногда объект приглашенного пользователя не может использовать адрес электронной почты из-за конфликта с контактным объектом (например, контактным объектом Outlook). В этом случае пользователю необходимо щелкнуть URL-адрес активации в приглашении, полученном по электронной почте.
 - Пользователь может выполнить вход с псевдонимом адреса электронной почты, по которому поступило приглашение (Псевдоним — это дополнительный адрес электронной почты, связанный с учетной записью электронной почты.) В этом случае пользователь должен щелкнуть URL-адрес активации в сообщении с приглашением.

## <a name="consent-experience-for-the-guest"></a>Условия предоставления согласия для гостей

Когда гостевая программа подписывается на доступ к ресурсам в партнерской организации впервые, они проходят по следующим страницам. 

1. Гость проверяет страницу **разрешений на проверку** , описывающую заявление о конфиденциальности в приглашенной Организации. Чтобы продолжить, пользователь должен **принять** использование своих сведений в соответствии с политиками конфиденциальности в приглашенной Организации.

   ![Снимок экрана со страницей "Проверить разрешения"](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Сведения о том, как администратор клиента может получить ссылку на заявление о конфиденциальности организации, см. в статье [How-to: Add your organization's privacy info in Azure Active Directory](https://aka.ms/adprivacystatement) (Практическое руководство. Добавление сведений о конфиденциальности организации в Azure Active Directory).

2. Если условия использования настроены, гостевой компьютер откроет и проверит условия использования, а затем выберет параметр **принять**. 

   ![Снимок экрана с новыми условиями использования](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Для настройки см. [условия использования](../governance/active-directory-tou.md) в разделе **Управление** > **организационными связями** > **условия использования**.

3. Если не указано иное, гость перенаправляется на панель доступа к приложениям, в которой перечислены приложения, к которым может получить доступ гость.

   ![Снимок экрана: панель доступа к приложениям](media/redemption-experience/myapps.png) 

В каталоге **приглашение гостя принимает** значение **Да**. При создании MSA в **источнике гостевой системы** отображается **учетная запись Майкрософт**. Дополнительные сведения о свойствах учетной записи гостевого пользователя см. в разделе [Свойства пользователя службы совместной работы Azure AD B2B](user-properties.md). 

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое служба совместной работы Azure AD B2B?](what-is-b2b.md)
- [Add Azure Active Directory B2B collaboration users in the Azure portal](add-users-administrator.md) (Добавление пользователей службы совместной работы Azure Active Directory B2B на портале Azure)
- [How do information workers add B2B collaboration users to Azure Active Directory?](add-users-information-worker.md) (Как информационные работники могут добавить пользователей для совместной работы B2B в Azure Active Directory)
- [Добавление пользователей службы совместной работы Azure Active Directory B2B с помощью PowerShell](customize-invitation-api.md#powershell)
- [Leave an organization as a guest user](leave-the-organization.md) (Выход гостевого пользователя из организации)
