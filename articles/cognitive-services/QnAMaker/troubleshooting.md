---
title: Устранение неполадок — QnA Maker
titleSuffix: Azure Cognitive Services
description: Проверенный список наиболее часто задаваемых вопросов о службе QnA Maker поможет вам быстрее приработать службу и с лучшими результатами.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 18d7e02689cc9c5fe9282a6a2456b8b1574ec85e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901643"
---
# <a name="troubleshooting-for-qna-maker"></a>Устранение неполадок для QnA Maker

Проверенный список наиболее часто задаваемых вопросов о службе QnA Maker поможет вам быстрее приработать службу и с лучшими результатами.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Как получить конечную точку службы QnAMaker

Конечная точка службы QnAMaker полезна в целях отладки при обращении в службу поддержки QnAMaker или UserVoice. Конечная точка является URL-адресом в следующем формате: https://your-resource-name.azurewebsites.net.

1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnAMaker на портале Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Выберите службу приложений, связанную с ресурсом QnA Maker. Как правило, имена совпадают.

     ![Выберите службу приложений QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. URL-адрес конечной точки доступен в разделе "Обзор"

    ![Конечная точка QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

## <a name="manage-the-knowledge-base"></a>Управление базой знаний

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Была случайно удалена часть службы QnA Maker. Что теперь делать?

Не удаляйте ни одну из служб Azure, созданных вместе с QnA Makerным ресурсом, таким как поиск или веб-приложение. Они необходимы для работы QnA Maker. Если удалить ее, QnA Maker перестанет работать правильно.

Все операции удаления являются безвозвратными, включая удаление пар вопросов и ответов, файлов, URL-адресов, пользовательских вопросов и ответов, баз знаний или ресурсов Azure. Убедитесь, что база знаний была экспортирована на странице **Параметры**, прежде чем удалять часть базы знаний.

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Почему для указанных мной URL-адресов или файлов не извлекаются пары "вопрос — ответ"?

В некоторых случаях QnA Maker не может автоматически извлечь содержимое в формате "вопрос — ответ" по допустимым URL-адресам. В таких случаях поместите нужное содержимое в TXT-файл и проверьте, сможет ли средство извлечь содержимое в таком формате. Кроме того, вы можете использовать интерфейс редактора для добавления содержимого в базу знаний на [портале QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Базу данных какого размера можно создать?

Допустимый размер базы знаний зависит от номера SKU, который вы выбрали для Поиска Azure при создании службы QnA Maker. Дополнительные сведения см. [здесь](./Tutorials/choosing-capacity-qnamaker-deployment.md).

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Почему ничего не отображается в раскрывающемся списке, когда я пытаюсь создать базу знаний?

Скорее всего, вы еще не создали службу QnA Maker в Azure. Щелкните [здесь](./How-To/set-up-qnamaker-service-azure.md), чтобы узнать, как это сделать.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Как поделиться базой знаний с другими пользователями?

Совместное использование работает на уровне службы QnA Maker, т. е. все базы знаний в службах предоставляются для общего доступа. [Здесь](./How-To/collaborate-knowledge-base.md) подробно описана совместная работа над базой знаний.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Можно ли предоставить базу знаний для редактирования участнику, который находится в другом клиенте AAD?

При совместном использовании используется механизм управления доступом на основе ролей (RBAC) Azure. Если вы можете предоставить общий доступ к _любому_ ресурсу в Azure другому пользователю, вы можете сделать то же самое с QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Предположим, у вас есть план службы приложений с 5 базами знаний QnA Maker. Можно ли предоставить 5 разным пользователям права на чтение и запись так, чтобы каждый из них мог использовать только 1 базу знаний QnA Maker?

Общий доступ предоставляется к службе QnA Maker в целом, а не к отдельным базам знаний.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Как изменить сообщение, которое отображается по умолчанию при отсутствии подходящего совпадения?

Это сообщение по умолчанию настраивается в службе приложений.
- Перейдите к ресурсу службы приложений на портале Azure.

![Служба приложений QnA Maker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Щелкните элемент **Параметры**.

![Параметры службы приложений QnA Maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Измените значение параметра **DefaultAnswer**.
- Перезапустите службу приложений.

![Перезапуск службы приложений QnA Maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Почему ничего не извлекается по ссылке на SharePoint?

Дополнительные сведения см. в разделе о [расположениях источников данных](./Concepts/knowledge-base.md#data-source-locations).

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Обновления, выполненные в базе знаний, не отразились в публикации. Почему так происходит?

Любую операцию изменения (обновление таблиц, тесты или настройки) нужно сохранить, чтобы она была опубликована. Не забудьте нажать кнопку **сохранить и обучить** после каждой операции редактирования.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Поддерживает ли база знаний форматированные данные или мультимедиа?

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Автоматическое извлечение файлов и URL-адресов в мультимедиа

* URL-адреса — возможность преобразования "HTML-Markdown" ограничена.
* Файлы — не поддерживаются

#### <a name="answer-text-in-markdown"></a>Текст ответа в Markdown
После установки QnA в базе знаний можно изменить текст Markdown ответа, чтобы включить ссылки на носитель, доступный по общедоступным URL-адресам.

### <a name="does-qna-maker-support-non-english-languages"></a>Поддерживает ли QnA Maker языки, отличные от английского?

Сведения о поддерживаемых языках вы найдете на [этой странице](./Overview/languages-supported.md).

Если у вас есть содержимое на нескольких языках, обязательно создайте отдельную службу для каждого языка.

## <a name="manage-service"></a>Управление службой

### <a name="when-should-i-restart-my-app-service"></a>Когда следует перезапустить службу приложений?

Обновите службу приложений, когда значок предупреждения рядом со значением версия для базы знаний в таблице **ключи конечных точек** на [странице](https://www.qnamaker.ai/UserSettings) **Параметры пользователя** .

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Моя существующая служба поиска удалена. Как это исправить?

Если удалить индекс Когнитивный поиск Azure, операция является окончательной и индекс нельзя будет восстановить.

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Я удалил `testkb` индекс в службе поиска. Как это исправить?

Старые данные невозможно восстановить. Создайте новый ресурс QnA Maker и снова создайте базу знаний.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Когда мне нужно обновить ключи конечной точки?

Обновляйте ключи конечной точки каждый раз, когда есть основания подозревать их компрометацию.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>Можно ли использовать один и тот же ресурс Azure Когнитивный поиск для баз знаний с несколькими языками?

Чтобы использовать несколько языков и несколько баз знаний, пользователю придется создать ресурс QnA Maker для каждого языка. При этом будет создана отдельная служба поиска Azure для каждого языка. Сочетание баз знаний на нескольких языках в одной службе поиска Azure приведет к снижению релевантности результатов.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Как изменить имя ресурса Azure Когнитивный поиск, используемого QnA Maker?

Имя ресурса Azure Когнитивный поиск — это QnA Maker имя ресурса, в конец которого добавляются случайные буквы. Из-за этого QnA Maker трудно различить несколько ресурсов службы "Поиск". Создайте отдельную службу поиска (наименовать ее как нужно) и подключите ее к службе QnA. Шаги аналогичны действиям, которые необходимо выполнить для [обновления службы поиска Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Когда QnA Maker возвращает `Runtime core is not initialized,` как устранить эту проблему?

Место на диске для службы приложений может быть заполнено. Действия по исправлению дискового пространства:

1. В [портал Azure](https://portal.azure.com)выберите службу приложений QnA Maker, а затем закройте службу.
1. Находясь в службе приложений, выберите **средства разработки**, затем **Дополнительные инструменты**, а затем **перейдите**. Откроется новое окно браузера.
1. Выберите **консоль отладки**, а затем **cmd** , чтобы открыть программу командной строки.
1. Перейдите в каталог _site/wwwroot/Data/QnAMaker/_ Directory.
1. Удалите все папки, имена которых начинаются с `rd`.

    **Не удаляйте** следующие:

    * Файл Кбидторанкермаппингс. txt
    * Файл Ендпоинтсеттингс. JSON
    * Папка Ендпоинткэйс

1. Запустите службу приложений.
1. Получите доступ к базе знаний, чтобы проверить, работает ли она сейчас.


## <a name="integrate-with-other-services-including-bots"></a>Интеграция с другими службами, в том числе ботами

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Нужна ли платформа Bot Framework для использования QnA Maker?

Нет, не нужно использовать [платформу Bot](https://github.com/Microsoft/botbuilder-dotnet) с QnA Maker. Однако QnA Maker предлагается как один из нескольких шаблонов в [службе Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Служба Azure Bot позволяет быстро разрабатывать интеллектуальные боты на платформе Microsoft Bot Framework, которые выполняются в бессерверной среде.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Как создать новый робот с QnA Maker?

Для создания бота в службе Azure Bot следуйте инструкциям из [этого](./Tutorials/create-qna-bot.md) документа.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Разделы справки использовать другую базу знаний с существующей службой Azure Bot?

Необходимо иметь следующие сведения о базе знаний:

* Идентификатор базы знаний.
* Имя пользовательского поддомена опубликованной конечной точки базы знаний, известное как `host`, найдено на странице **параметров** после публикации.
* Опубликованный ключ конечной точки базы знаний — находится на странице **параметров** после публикации.

Используя эти сведения, перейдите в службу приложений Bot в портал Azure. В разделе **Параметры — > Конфигурация — > Параметры приложения**измените эти значения.

Ключ конечной точки в базе знаний помечается `QnAAuthkey` в службе ABS.

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Может ли два или больше клиентских приложений совместно использовать базу знаний?

Да, база знаний может запрашиваться с любого числа клиентов. Если ответ базы знаний оказывается слишком длительным или истекает, рассмотрите возможность обновления уровня служб для службы приложений, связанной с базой знаний.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Как можно внедрить службу QnA Maker на веб-сайте?

Чтобы внедрить службу QnA Maker в качестве элемента управления веб-чатом, сделайте следующее:

1. Создайте бота вопросов и ответов, следуя [этим](./Tutorials/create-qna-bot.md) инструкциям.
2. Включите веб-чат, выполнив шаги из [этой статьи](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat).

## <a name="data-storage"></a>Хранилище данных

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Какие данные сохраняются и где они хранятся?

При создании службы QnA Maker вы указываете регион Azure. Базы знаний и файлы журналов хранятся в этом регионе.
