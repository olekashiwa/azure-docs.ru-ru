---
title: Руководство. Создание экспортированных данных из Управления затратами Azure и управление этими данными
description: В этой статье показано, как создавать экспортированные данные в службе "Управление затратами Azure" и управлять ими, чтобы использовать эти данные во внешних системах.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: cost-management-billing
manager: jasonh
ms.custom: seodec18
ms.openlocfilehash: 76ee5aba0f1d0769e15a5969409dfef2a018e477
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75984039"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Руководство. Создание задачи для экспорта данных и управление экспортированными данными

Если вы изучили учебник по анализу затрат, вы знакомы с процедурой загрузки данных из системы управления затратами вручную. Но можно создать повторяющуюся задачу, которая будет ежедневно, еженедельно или ежемесячно автоматически экспортировать ваши данные из системы управления затратами в хранилище Azure. Экспортированные данные представлены в формате CSV и содержат все сведения, собираемые системой управления затратами. Затем можно использовать экспортированные данные в хранилище Azure в работе с внешними системами и объединять их с собственными пользовательскими данными. Кроме того, можно использовать экспортированные данные во внешней системе, например на панели мониторинга или в другой финансовой системе.

Сведения о создании запланированной операции экспорта данных о затратах Azure в хранилище Azure см. в видео [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Как запланировать операции экспорта в хранилище с помощью службы "Управление затратами Azure").

Примеры в этом руководстве по шагам демонстрируют экспорт ваших данных из системы управления затратами, а затем проверку успешности экспорта данных.

В этом руководстве описано следующее.

> [!div class="checklist"]
> * Создание ежедневного экспорта
> * Проверка сбора данных

## <a name="prerequisites"></a>Предварительные требования
Экспорт данных доступен в различных типах учетной записи Azure, включая клиентов [Соглашения Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) и клиентов [Клиентского соглашения Майкрософт](get-started-partners.md). Полный список поддерживаемых типов учетных записей см. в статье [Understand Cost Management data](understand-cost-mgt-data.md) (Интерпретация данных службы "Управление затратами"). Для экспорта данных пользователем или группой поддерживаются следующие разрешения или области Azure на подписку. См. [основные сведения об областях и работе с ними](understand-work-scopes.md).

- Владелец: может создавать, изменять или удалять операции экспорта для подписки.
- Участник: может создавать, изменять или удалять свои запланированные операции экспорта. Может изменять имена запланированных операций экспорта, созданных другими пользователями.
- Читатель: может просматривать операции экспорта, для которых имеет разрешение.

Для учетных записей хранения Azure:
- Чтобы изменить настроенную учетную запись хранения, требуются разрешения на запись, вне зависимости от разрешений на экспорт.
- Ваша учетная запись хранения Azure должна быть настроена для использования хранилища BLOB-объектов или хранилища файлов.

## <a name="sign-in-to-azure"></a>Вход в Azure
Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Создание ежедневного экспорта

Чтобы создать или запланировать операцию экспорта либо же просмотреть сведения о ней, откройте требуемую область на портале Azure и выберите в меню **Анализ затрат**. Например, перейдите к разделу **Подписки**, выберите подписку из списка и щелкните в меню **Анализ затрат**. В верхней части страницы анализа затрат выберите **Экспорт** и параметр экспорта. Например, выберите **Запланировать экспорт**.  

> [!NOTE]
> - Помимо подписок можно экспортировать группы ресурсов, учетные записи, подразделения и регистрации. См. [основные сведения об областях и работе с ними](understand-work-scopes.md).
>- Войдя в систему как партнер в области счета выставления счетов или в клиентском арендаторе, вы можете экспортировать данные в учетную запись хранения Azure, связанную с учетной записью хранения партнера. При этом в арендаторе CSP должна быть активная подписка.
>


Выберите **Добавить**, введите имя для задачи экспорта и выберите параметр **Ежедневный экспорт расходов за текущий месяц**. Выберите **Далее**.

![Пример создания задачи экспорта, в котором показан тип экспорта](./media/tutorial-export-acm-data/basics_exports.png)

Укажите подписку для учетной записи хранения Azure, а затем выберите свою учетную запись хранения.  Укажите контейнер хранилища и путь к каталогу для экспорта файла. Выберите **Далее**.

![Пример создания задачи экспорта, в котором показаны данные учетной записи хранения](./media/tutorial-export-acm-data/storage_exports.png)

Просмотрите данные об экспорте и выберите **Создать**.

Новый экспорт отобразится в списке экспорта. По умолчанию новые задачи экспорта включены. Если требуется отключить или удалить запланированный экспорт, выберите любой пункт в списке, а затем **Выключить** или **Удалить**.

Изначально до начала экспорта может потребоваться 1–2 часа. Однако прежде чем данные отобразятся в экспортированных файлах, может пройти до четырех часов.

### <a name="export-schedule"></a>Расписание экспорта

Запланированный экспорт зависит от времени и дня недели, когда изначально был создан экспорт. При создании запланированного экспорта он выполняется с той же частотой для каждого последующего выхода экспорта. Например, для экспорта с начала месяца с ежедневным выполнением, экспорт выполняется каждый день. Аналогично для еженедельного экспорта экспорт выполняется каждую неделю в тот же день, когда он был запланирован. Точное время доставки при экспорте не гарантируется, а экспортированные данные доступны в течение четырех часов времени выполнения."
Каждый экспорт создает новый файл, поэтому старые экспорты не перезаписываются.

Существует два типа параметров экспорта.

**Ежедневный экспорт расходов за текущий месяц**. Первоначальный экспорт выполняется немедленно. Последующий экспорт выполняется на следующий день в то же время, что и первоначальный экспорт. Последние данные агрегируются из предыдущего ежедневного экспорта.

**Настраиваемый**. Позволяет планировать еженедельный и ежемесячный экспорт с соответствующими параметрами. *Первоначальный экспорт выполняется немедленно.*

Если у вас есть подписка с оплатой по мере использования, подписка MSDN или подписка Visual Studio, ваш период выставления счетов может не совпадать с календарным месяцем. Для таких типов подписок и групп ресурсов можно создать операцию экспорта, которая соответствует периоду выставления счетов или календарным месяцам. Чтобы создать операцию экспорта, которая соответствует ежемесячному выставлению счетов, выберите **Настраиваемый**, а затем выберите **Период выставления счетов до указанной даты**.  Чтобы создать операцию экспорта, соответствующую календарному месяцу, выберите **С начала месяца**.
>
>

![Новый экспорт. Вкладка "Основные сведения" отображает настраиваемый еженедельный выбор с начала недели.](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Проверка сбора данных

Вы легко можете убедиться, что ваши данные из системы управления расходами были собраны, и просмотреть экспортированный CSV-файл с помощью обозревателя службы хранилища Azure.

В списке экспорта выберите имя учетной записи хранения. На странице учетной записи хранения в Обозревателе выберите "Открыть". Если появится поле подтверждения, выберите **Да**, чтобы открыть файл в Обозревателе службы хранилища Azure.

![Страница учетной записи хранения со справочными данными и ссылкой "Открыть в обозревателе"](./media/tutorial-export-acm-data/storage-account-page.png)

В обозревателе службы хранилища перейдите в контейнер, который требуется открыть, и выберите папку, соответствующую текущему месяцу. Отобразится список CSV-файлов. Выберите файл, а затем — **Открыть**.

![Справочные данные, отображаемые в Обозревателе службы хранилища](./media/tutorial-export-acm-data/storage-explorer.png)

Файл открывается с помощью программы или приложения, которое задано для открытия файлов с расширением CSV. Ниже приведен пример в Excel.

![Пример экспортированных данных CSV в Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Доступ к экспортированным данным из других систем

Одной из целей экспорта данных системы управления затратами является возможность доступа к данным из внешних систем. Можно использовать систему панелей мониторинга или другую финансовую систему. Такие системы значительно варьируются, поэтому приводить примеры нецелесообразно.  Однако для начала можно осуществлять доступ к данным из ваших приложений, следуя инструкциям в разделе [Вводные сведения о службе хранилища Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание ежедневного экспорта
> * Проверка сбора данных

Перейдите к следующему руководству, чтобы повысить эффективность своей работы благодаря выявлению незадействованных или недостаточно используемых ресурсов.

> [!div class="nextstepaction"]
> [Просмотр рекомендаций по оптимизации и реагирование на них](tutorial-acm-opt-recommendations.md)
