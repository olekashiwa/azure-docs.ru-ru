---
title: Руководство по настройке ServiceNow для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить автоматическую подготовку и отзыв учетных записей пользователей из Azure AD в ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 5e954de97e6573e330c42915b44bbc0c40abc54d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063254"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Учебник. Настройка ServiceNow для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в ServiceNow и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп к [ServiceNow](https://www.servicenow.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в ServiceNow
> * Удалить пользователей в ServiceNow, если им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и ServiceNow
> * Предоставление группам и членству в группах в ServiceNow
> * [Единый вход](servicenow-tutorial.md) в ServiceNow (рекомендуется)

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* [Экземпляр ServiceNow](https://www.servicenow.com/) Калгари или более поздней версии
* [Экземпляр ServiceNow Express](https://www.servicenow.com/) с помощью Хельсинки или более поздней версии
* Учетная запись пользователя в ServiceNow с ролью администратора

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания подготовки
1. Узнайте [, как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка ServiceNow для поддержки подготовки с помощью Azure AD

1. Найдите имя экземпляра ServiceNow. Имя экземпляра можно найти в URL-адресе, который используется для доступа к ServiceNow. В приведенном ниже примере имя экземпляра — dev35214.

![Экземпляр ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Получите учетные данные для администратора в ServiceNow. Перейдите к профилю пользователя в ServiceNow и убедитесь, что у пользователя есть роль администратора. 

![Роль администратора ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление ServiceNow из коллекции приложений Azure AD

Добавьте ServiceNow из коллекции приложений Azure AD, чтобы начать управление подготовкой в ServiceNow. Если вы ранее настроили ServiceNow для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей, которые будут находиться в области подготовки 

Служба подготовки Azure AD позволяет определить, кто будет подготовлен в соответствии с назначением приложения, или на основе атрибутов пользователя или группы. Если вы решили указать, кто будет подготовлен для приложения на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md) , чтобы назначить пользователей и группы для приложения. Если выбрать область, для которой будет выполняться подготовка на основе только атрибутов пользователя или группы, можно использовать фильтр области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп в ServiceNow необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью доступа по умолчанию исключаются из подготовки и будут помечены как неэффективное в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , чтобы добавить дополнительные роли. 

* Запуск Small. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если для параметра область подготовки задано значение назначенные пользователи и группы, это можно контролировать, назначив приложению одного или двух пользователей или групп. Если для параметра scope задано значение все пользователи и группы, можно указать [Фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Шаг 5. Настройка автоматической подготовки пользователей в ServiceNow 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в TestApp на основе назначений пользователей и групп в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей для ServiceNow в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **ServiceNow**.

    ![Ссылка на ServiceNow в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите учетные данные администратора ServiceNow и имя пользователя. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к ServiceNow. В случае сбоя подключения убедитесь, что учетная запись ServiceNow имеет разрешения администратора, и повторите попытку.

    ![Подготовка](./media/servicenow-provisioning-tutorial/provisioning.png)

6. В поле **уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, и установите флажок **Отправить уведомление по электронной почте при возникновении сбоя** .

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с ServiceNow**.

9. В разделе **сопоставление атрибутов** проверьте пользовательские атрибуты, которые синхронизированы из Azure AD в ServiceNow. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в ServiceNow для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API ServiceNow поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с ServiceNow**.

11. В разделе **сопоставление атрибутов** Проверьте атрибуты группы, которые синхронизированы из Azure AD в ServiceNow. Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в ServiceNow для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для ServiceNow, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать в ServiceNow, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **области** в разделе **параметров** . Выполнение начального цикла занимает больше времени, чем последующие циклы, что происходит примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , чтобы определить, какие пользователи были подготовлены успешно или неудачно
2. Просмотрите [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , чтобы просмотреть состояние цикла подготовки и его завершения
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о состояниях карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
* **Инвалидлукупреференце:** При подготовке определенных атрибутов, таких как отдел и расположение в ServiceNow, значения должны уже существовать в ссылочной таблице в ServiceNow. Например, у вас могут быть два местоположения (Сиэтл, Лос-Анджелес) и три отдела (продажи, финансы, маркетинг) в таблице **INSERT Table name** в ServiceNow. При попытке подготовить пользователя, где его отдел имеет значение "продажи", а "расположение" — "Сиэтл", он будет успешно подготовлен. При попытке подготовить пользователя с помощью отдела "продажи" и расположения "LA" пользователь не будет подготовлен. Необходимо либо добавить LA в ссылочную таблицу в ServiceNow, либо обновить атрибут пользователя в Azure AD, чтобы он соответствовал формату ServiceNow. 
* **Ентрижоинингпропертивалуеисмиссинг:** Проверьте [сопоставления атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) , чтобы обозначить соответствующий атрибут. Это значение должно присутствовать у пользователя или группы, которые вы пытаетесь подготавливать. 
* Ознакомьтесь с [API-интерфейсом ServiceNow для протокола SOAP](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) , чтобы ознакомиться с любыми требованиями или ограничениями (например, Format, чтобы указать код страны для пользователя).
* Для некоторых развертываний ServiceNow требуется разрешить диапазоны IP-адресов для службы подготовки Azure AD. Зарезервированные диапазоны IP-адресов для службы подготовки Azure AD можно найти [здесь по адресу](https://www.microsoft.com/download/details.aspx?id=56519) "азуреактиведиректоридомаинсервицес".
* Подготовка пользователей в облаке для государственных организаций ServiceNow в настоящее время не поддерживается.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
