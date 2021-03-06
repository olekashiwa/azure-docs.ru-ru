---
title: Внедрение клиента Анализатора Интернета | Документация Майкрософт
description: Из этой статьи вы узнаете, как внедрить клиент JavaScript Анализатора Интернета в приложение.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896385"
---
# <a name="embed-the-internet-analyzer-client"></a>Внедрение клиента Анализатора Интернета

Из этой статьи вы узнаете, как внедрить клиент JavaScript в приложение. Установка этого клиента необходима для выполнения тестов и получения аналитики системы показателей. **Клиент JavaScript, зависящий от профиля, предоставляется после настройки первого теста.** После этого можно добавлять или удалять тесты в этом профиле без внедрения нового скрипта. Дополнительные сведения об Анализаторе Интернета см. в [обзоре](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Эта общедоступная предварительная версия предоставляется без соглашения об уровне обслуживания и не должна использоваться для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Перед началом работы

Для правильной работы Анализатора Интернета требуется доступ к Azure и другим службам Майкрософт. Перед внедрением клиента разрешите сетевой доступ к `fpc.msedge.net` и всем URL-адресам предварительно настроенных конечных точек (видимым в [CLI](internet-analyzer-cli.md)).

## <a name="find-the-client-script-url"></a>Поиск URL-адреса клиентского сценария

URL-адрес скрипта можно найти с помощью либо портала Azure, либо Azure CLI после настройки теста. Дополнительные сведения см. в разделе о [создании ресурса Анализатора Интернета](internet-analyzer-create-test-portal.md).

Вариант 1. На портале Azure используйте [эту ссылку](https://aka.ms/InternetAnalyzerPreviewPortal), чтобы открыть страницу портала предварительной версии для Анализатора Интернета Azure. Чтобы просмотреть URL-адрес скрипта, перейдите к профилю Анализатора Интернета и выберите **Параметры > Настройка**.

Вариант 2. С помощью Azure CLI проверьте свойство `scriptFileUri`.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Сведения о клиенте

Скрипт создается специально для профиля и тестов. После загрузки скрипт будет выполняться с задержкой в 2 секунды. Сначала он обращается к службе "Анализатор Интернета", чтобы получить список конечных точек, настроенных в тестах. Затем он выполняет измерения и передает результаты с указанием времени обратно в службу "Анализатор Интернета".

## <a name="client-examples"></a>Примеры клиента

В этих примерах показано несколько основных методов внедрения клиента JavaScript в веб-страницу или приложение. В качестве примера идентификатора профиля для URL-адреса скрипта используется `0bfcb32638b44927935b9df86dcfe397`.

### <a name="run-on-page-load"></a>Запуск при загрузке страницы
Самый простой способ — использовать тег скрипта внутри блока метатега. Этот тег будет выполнять скрипт один раз для каждой загрузки страницы.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Дополнительная информация

[Вопросы и ответы об Анализаторе Интернета](internet-analyzer-faq.md)
