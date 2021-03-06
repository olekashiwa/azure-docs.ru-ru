---
title: Глоссарий по защите идентификации Azure Active Directory
description: Глоссарий по защите идентификации Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232344"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Глоссарий по защите идентификации Azure Active Directory

### <a name="at-risk-user"></a>Под угрозой (пользователь)
Пользователь с одним или несколькими активными обнаруженными рисками. 

### <a name="atypical-sign-in-location"></a>Нетипичное расположение входа
Вход из географического расположения, которое не является типичным для конкретного пользователя, схожих пользователей или клиента.

### <a name="azure-ad-identity-protection"></a>Защита идентификации Azure AD
Модуль безопасности Azure Active Directory, обеспечивающий объединенное представление об обнаружении рисков и потенциальных уязвимостях, влияющих на удостоверения Организации.

### <a name="conditional-access"></a>условный доступ;
Политика для защиты доступа к ресурсам. Правила условного доступа хранятся в Azure Active Directory и оцениваются с помощью Azure AD перед предоставлением доступа к ресурсу.  Примерами правил являются ограничения доступа на основе расположения пользователя, работоспособности устройства или метода проверки подлинности пользователя.

### <a name="credentials"></a>Учетные данные
Данные, которые включают в себя идентификацию и проверку идентификации, используемой для получения доступа к локальным и сетевым ресурсам. Примерами учетных данных являются имена пользователей и пароли, смарт-карты и сертификаты.

### <a name="event"></a>Событие
Запись действия в Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Ложное срабатывание (обнаружение рисков)
Состояние обнаружения рисков, заданное вручную пользователем защиты идентификации, указывающее, что обнаружение рисков было исследовано и ошибочно отмечено как обнаружение рисков.

### <a name="identity"></a>Удостоверение
Лицо или организация, которые должны быть проверены с помощью средств проверки подлинности на основе определенных условий, таких как пароль или сертификат.

### <a name="identity-risk-detection"></a>Обнаружение рисков удостоверений
Событие Azure AD, которое было отмечено как аномальное с помощью защиты идентификации, и может означать, что удостоверение скомпрометировано.

### <a name="ignored-risk-detection"></a>Игнорируется (обнаружение рисков)
Состояние обнаружения рисков, заданное вручную пользователем защиты идентификации, указывающее, что обнаружение рисков закрывается без выполнения действия исправления.

### <a name="impossible-travel-from-atypical-locations"></a>Невозможно переместиться из нетипичных расположений
Обнаружение рисков срабатывает при обнаружении двух входов для одного пользователя, где по крайней мере один из них находится в нетипичном расположении входа, а время между попытками входа короче минимального времени, которое необходимо для физического переезда между ними. расположения.  

### <a name="investigation"></a>Исследование
Процесс просмотра действий, журналов и других релевантных сведений, относящихся к обнаружению рисков с целью принятия решения о необходимости выполнения действий по исправлению или устранению, сведения о том, каким образом и как скомпрометировано удостоверение. удостоверение было использовано.

### <a name="leaked-credentials"></a>Утерянные учетные данные
Обнаружение рисков, активируемое при обнаружении учетных данных текущего пользователя (имени пользователя и пароля), опубликованных в виде темной веб-страниц нашими исследователими.

### <a name="mitigation"></a>Устранение
Действие, позволяющее ограничить или исключить возможность использования злоумышленником скомпрометированного удостоверения или устройства без восстановления их безопасного состояния. Устранение рисков не приводит к устранению предыдущих обнаружений рисков, связанных с удостоверением или устройством.

### <a name="multi-factor-authentication"></a>Многофакторная проверка подлинности
Метод проверки подлинности, требующий двух или более способов проверки подлинности, включая следующее: предоставляемые пользователем объекты, например сертификаты; известные только пользователям сведения, например имена пользователей, пароли или парольные фразы; физические атрибуты, например отпечаток большого пальца; персональные атрибуты, например личная подпись.

### <a name="offline-detection"></a>Обнаружение в автономном режиме
Обнаружение аномалий и оценки риска события, такого как попытка входа после того, как событие уже возникло.

### <a name="policy-condition"></a>Условие политики
Часть политики безопасности, которая определяет сущности (группы, пользователи, приложения, платформы устройств, состояния устройств, диапазоны IP-адресов, типы клиентов), включаемые в политику или исключаемые из нее.

### <a name="policy-rule"></a>Правило политики
Часть политики безопасности, которая описывает обстоятельства, которые активируют политику, и действия, выполняемые при активации политики.

### <a name="prevention"></a>Предотвращение
Действие для предотвращения нанесения ущерба организации путем использования удостоверения или устройства, которое ранее предположительно или фактически было скомпрометировано. Действие предотвращения не защищает устройство или удостоверение и не разрешает предыдущие обнаружения рисков.

### <a name="privileged-user"></a>Привилегированный (пользователь)
Пользователь, который во время обнаружения риска обладает постоянными или временными разрешениями администратора для одного или нескольких ресурсов в Azure Active Directory, таких как глобальный администратор, администратор выставления счетов, администратор служб, администратор пользователей и пароль. Административ. 

### <a name="real-time"></a>В режиме реального времени
См. "Обнаружение в режиме реального времени".

### <a name="real-time-detection"></a>Обнаружение в режиме реального времени.
Обнаружение аномалий и оценки риска события, такого как попытка входа до разрешения продолжения события.

### <a name="remediated-risk-detection"></a>Исправлено (обнаружение рисков)
Состояние обнаружения рисков автоматически задается защитой идентификации, указывая на то, что обнаружение рисков было исправлено с помощью стандартного действия по исправлению для этого типа обнаружения рисков. Например, при сбросе пароля пользователя многие обнаружения рисков, указывающие на то, что предыдущий пароль был скомпрометирован, автоматически исправляются.

### <a name="remediation"></a>Исправление
Действие для защиты удостоверения или устройства, которое было ранее предположительно или фактически скомпрометировано. Действие исправления восстанавливает состояние "удостоверение" или "устройство" в безопасном состоянии и разрешает предыдущие обнаружения рисков, связанные с удостоверением или устройством.

### <a name="resolved-risk-detection"></a>Разрешено (обнаружение рисков)
Состояние обнаружения рисков, заданное пользователем для защиты идентификации вручную, которое указывает, что пользователь выполнил соответствующее действие по исправлению вне защиты идентификации и что обнаружение рисков должно считаться закрытым.

### <a name="risk-detection-status"></a>Состояние обнаружения рисков
Свойство обнаружения рисков, указывающее, является ли событие активным, и, если оно закрыто, причина его закрытия.

### <a name="risk-detection-type"></a>Тип обнаружения риска
Категория для обнаружения рисков, указывающая тип аномалии, которая привела к тому, что событие будет считаться рискованным.

### <a name="risk-level-risk-detection"></a>Уровень риска (обнаружение рисков)
Указание (высокая, средняя или низкая) серьезности обнаружения рисков, помогающее пользователям защиты идентификации расстановка приоритетов действий, выполняемых для снижения риска в Организации. 

### <a name="risk-level-sign-in"></a>Уровень риска (вход)
Обозначение уровня вероятности ("Высокий", "Средний" или "Низкий") того, что при конкретной попытке входа удостоверение пользователя будет использоваться другим лицом.

### <a name="risk-level-user-compromise"></a>Уровень риска (компрометация пользователя)
Обозначение уровня вероятности ("Высокий", "Средний" или "Низкий") компрометации удостоверения.

### <a name="risk-level-vulnerability"></a>Уровень риска (уязвимость)
Обозначение уровня серьезности уязвимости ("Высокий", "Средний" или "Низкий"), чтобы помочь пользователям модуля защиты идентификации расставить приоритеты действий, которые необходимо предпринять, чтобы уменьшить риск для организации.

### <a name="secure-identity"></a>Безопасное (удостоверение)
Выполнение действия исправления, например смена пароля или повторное создание образа компьютера, для восстановления безопасного состояния скомпрометированного удостоверения.

### <a name="security-policy"></a>Политика безопасности
Коллекция правил и условий политики. Политика может применяться к сущностям, таким как пользователи, группы, приложения, устройства, платформы устройств, состояния устройств, диапазоны IP-адресов и типы клиентов Auth2.0. После включения политика проходит оценку каждый раз, когда включенной в политику сущности выдается маркер для ресурса.

### <a name="sign-in-v"></a>Выполнить вход
Проверить подлинность удостоверения в Azure Active Directory.

### <a name="sign-in-n"></a>Вход
Процесс или действие проверки подлинности удостоверения в Azure Active Directory и событие, которое записывает эту операцию.

### <a name="sign-in-from-anonymous-ip-address"></a>Вход с анонимных IP-адресов
Обнаружение рисков, активируемое после успешного входа с IP-адреса, определенного как IP-адрес анонимного прокси-сервера.

### <a name="sign-in-from-infected-device"></a>Вход с инфицированных устройств
Обнаружение рисков активируется, когда выполняется вход с IP-адреса, который используется одним или несколькими скомпрометированными устройствами, которые активно пытаются связаться с сервером-Bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Вход с IP-адресов с подозрительными действиями
Обнаружение рисков, активируемое после успешного входа в систему с IP-адреса с большим количеством неудачных попыток входа в несколько учетных записей пользователей за короткий период времени.

### <a name="sign-in-from-unfamiliar-location"></a>Вход из неизвестного расположения
Обнаружение рисков активируется, когда пользователь успешно входит в новое место (IP, Широта, Долгота и ASN).

### <a name="sign-in-risk"></a>Риск входа
См. "Уровень риска" (вход)

### <a name="sign-in-risk-policy"></a>Sign-in risk policy
Политика условного доступа, которая оценивает риск для конкретного входа и применяет меры по устранению на основе предопределенных условий и правил.

### <a name="user-compromise-risk"></a>Риск компрометации пользователя
См. "Уровень риска" (компрометация пользователя)

### <a name="user-risk"></a>Риск пользователя
См. "Уровень риска" (компрометация пользователя)

### <a name="user-risk-policy"></a>Политика риска пользователя
Политика условного доступа, которая учитывает вход и применяет меры по устранению на основе предопределенных условий и правил.

### <a name="users-flagged-for-risk"></a>Пользователи, помеченные для события риска
Пользователи с обнаруженными рисками (активные или исправленные);

### <a name="vulnerability"></a>Уязвимость
Конфигурация или условие в Azure Active Directory, которое делает каталог уязвимым к атакам или угрозам.

## <a name="see-also"></a>См. также

- [Защита идентификации Azure Active Directory.](../active-directory-identityprotection.md)
