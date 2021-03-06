---
title: Запись трассировки браузера для устранения неполадок | Документация Майкрософт
description: Запись сведений о сети из трассировки браузера для облегчения устранения неполадок портал Azure.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310702"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Запись трассировки браузера для устранения неполадок

Если вы можете устранить неполадки с портал Azure и вам нужно обратиться в службу поддержки Майкрософт, мы рекомендуем сначала записать трассировку браузера и некоторые дополнительные сведения. Собранные сведения могут предоставлять важные сведения о портале в момент возникновения проблемы. Выполните действия, описанные в этой статье, для средств разработчика в используемом браузере: Google Chrome или Microsoft (Chromium), Microsoft ребр (Еджехтмл) или Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome и Microsoft ребр (Chromium)

Google Chrome и Microsoft ребр (Chromium) основаны на проекте с [открытым исходным кодом Chromium](https://www.chromium.org/Home). В следующих шагах показано, как использовать средства разработчика, которые очень похожи в двух браузерах. Дополнительные сведения см. в разделе [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) and [Microsoft ребр (Chromium) средства для разработчиков](/microsoft-edge/devtools-guide-chromium).

1. Войдите на [портал Azure](https://portal.azure.com). _Прежде чем_ начать трассировку, важно выполнить вход, чтобы трассировка не содержала конфиденциальной информации, связанной с входом в систему. 

1. Начните запись действий, выполняемых на портале, с помощью [средства записи действий](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. На портале перейдите к шагу, который находится перед тем, как возникла проблема.

1. Нажмите клавишу F12 или выберите ![снимок экрана значка параметров браузера](media/capture-browser-trace/chromium-icon-settings.png) > **другие средства** > **средства разработчика**.

1. По умолчанию браузер сохраняет сведения о трассировке только для текущей загруженной страницы. Задайте следующие параметры, чтобы браузер поддерживал все данные трассировки, даже если для воспроизведения требуется несколько страниц:

    1. Перейдите на вкладку **сеть** , а затем выберите **сохранить журнал**.

          ![Снимок экрана: "сохранить журнал"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Перейдите на вкладку **консоль** , выберите **Параметры консоли**, а затем выберите **сохранить журнал**. Снова выберите **Параметры консоли** , чтобы закрыть панель Параметры.

          ![Снимок экрана: "сохранить журнал"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Перейдите на вкладку **сеть** , а затем выберите **отключить запись журнала сети** и **очистить**.

    ![Снимок экрана: "закончить запись журнала сети" и "очистить"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Выберите **записать сетевой журнал**, а затем Воспроизведите ошибку на портале.

    ![Снимок экрана: "Запуск сеанса профилирования"](media/capture-browser-trace/chromium-start-session.png)

    Вы увидите выходные данные сеанса, как показано на следующем рисунке.

    ![Снимок экрана результатов трассировки браузера](media/capture-browser-trace/chromium-browser-trace-results.png)

1. После повторного создания непредвиденного поведения портала выберите пункт " **отключить запись журнала сети**", а затем выберите **Export Har (экспорт** ) и сохраните файл.

    ![Снимок экрана: "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Отключите средство записи действий и сохраните файл.

1. Вернитесь на панель инструментов разработчика браузера, выберите вкладку **консоль** . Щелкните правой кнопкой мыши, выберите **Сохранить как...** и сохраните выходные данные консоли в текстовом файле.

    ![Снимок экрана с выводом на консоль](media/capture-browser-trace/chromium-console-select.png)

1. Упакуйте файл HAR, выходные данные консоли и запись экрана в сжатом формате, например ZIP, и предоставьте общий доступ к ним с помощью службы поддержки Майкрософт.

## <a name="microsoft-edge-edgehtml"></a>Microsoft ребро (Еджехтмл)

Ниже показано, как использовать средства разработчика в Microsoft ребр (Еджехтмл). Дополнительные сведения см. в [статье Microsoft ребр (еджехтмл) средства для разработчиков](/microsoft-edge/devtools-guide).

1. Войдите на [портал Azure](https://portal.azure.com). _Прежде чем_ начать трассировку, важно выполнить вход, чтобы трассировка не содержала конфиденциальной информации, связанной с входом в систему. 

1. Начните запись действий, выполняемых на портале, с помощью [средства записи действий](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. На портале перейдите к шагу, который находится перед тем, как возникла проблема.

1. Нажмите клавишу F12 или выберите ![снимок экрана значка параметров браузера](media/capture-browser-trace/edge-icon-settings.png) > **другие средства** > **средства разработчика**.

1. По умолчанию браузер сохраняет сведения о трассировке только для текущей загруженной страницы. Задайте следующие параметры, чтобы браузер поддерживал все данные трассировки, даже если для воспроизведения требуется несколько страниц:

    1. Перейдите на вкладку **сеть** и снимите флажок **Очистить записи при переходе**.

          ![Снимок экрана: "очистить записи при навигации"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Перейдите на вкладку **консоль** , а затем выберите **сохранить журнал**.

          ![Снимок экрана: "сохранить журнал"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Перейдите на вкладку **сеть** , выберите команду **прерывать сеанс профилирования** и **очистите сеанс**.

    ![Снимок экрана: "Завершение сеанса профилирования" и "очистка сеанса"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Выберите **начать сеанс профилирования**, а затем Воспроизведите ошибку на портале.

    ![Снимок экрана: "Запуск сеанса профилирования"](media/capture-browser-trace/edge-start-session.png)

    Вы увидите выходные данные сеанса, как показано на следующем рисунке.

    ![Снимок экрана результатов трассировки браузера](media/capture-browser-trace/edge-browser-trace-results.png)

1. После повторного создания непредвиденного поведения портала выберите **прерывать сеанс профилирования**, а затем выберите **экспортировать как HAR** и сохраните файл.

    ![Снимок экрана: "экспортировать как HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Отключите средство записи действий и сохраните файл.

1. Вернитесь на панель средства разработчика браузера, перейдите на вкладку **консоль** и разверните окно. Поместите курсор в начало выходных данных консоли, а затем выделите все содержимое выходных данных. Щелкните правой кнопкой мыши, выберите **Копировать**и сохраните выходные данные консоли в текстовый файл.

    ![Снимок экрана с выводом на консоль](media/capture-browser-trace/edge-console-select.png)

1. Упакуйте файл HAR, выходные данные консоли и запись экрана в сжатом формате, например ZIP, и предоставьте общий доступ к ним с помощью службы поддержки Майкрософт.

## <a name="apple-safari"></a>Apple Safari;

Ниже описано, как использовать средства разработчика в Apple Safari. Дополнительные сведения см. в разделе [Safari средства для разработчиков Overview](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Включение средств разработчика в Apple Safari:

    1. Выберите **Safari**и щелкните **предпочтения**.

        ![Снимок экрана параметров Safari](media/capture-browser-trace/safari-preferences.png)

    1. Перейдите на вкладку **Дополнительно** , а затем **в строке меню выберите команду Отобразить разработку**.

        ![Снимок экрана с дополнительными настройками Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Войдите на [портал Azure](https://portal.azure.com). _Прежде чем_ начать трассировку, важно выполнить вход, чтобы трассировка не содержала конфиденциальной информации, связанной с входом в систему. 

1. Начните запись действий, выполняемых на портале. Дополнительные сведения см. в статье [запись экрана на компьютере Mac](https://support.apple.com/HT208721).

1. На портале перейдите к шагу, который находится перед тем, как возникла проблема.

1. Выберите **Разработка**, а затем выберите пункт **отобразить веб-инспектор**.

    ![Снимок экрана: "отображение веб-инспектора"](media/capture-browser-trace/safari-show-web-inspector.png)

1. По умолчанию браузер сохраняет сведения о трассировке только для текущей загруженной страницы. Задайте следующие параметры, чтобы браузер поддерживал все данные трассировки, даже если для воспроизведения требуется несколько страниц:

    1. Перейдите на вкладку **сеть** , а затем выберите **сохранить журнал**.

          ![Снимок экрана: "сохранить журнал"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Перейдите на вкладку **консоль** , а затем выберите **сохранить журнал**.

          ![Снимок экрана: "сохранить журнал"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Перейдите на вкладку **сеть** и выберите **очистить сетевые элементы**.

    ![Снимок экрана: "очистить элементы сети"](media/capture-browser-trace/safari-clear-session.png)

1. Воспроизведите ошибку на портале. Вы увидите выходные данные сеанса, как показано на следующем рисунке.

    ![Снимок экрана результатов трассировки браузера](media/capture-browser-trace/safari-browser-trace-results.png)

1. После воспроизведения непредвиденного поведения портала выберите **Экспорт** и сохраните файл.

    ![Снимок экрана: "экспорт"](media/capture-browser-trace/safari-network-export-har.png)

1. Останавливает средство записи экрана и сохраняет файл.

1. Вернитесь на панель средства разработчика браузера, перейдите на вкладку **консоль** и разверните окно. Поместите курсор в начало выходных данных консоли, а затем выделите все содержимое выходных данных. Чтобы скопировать выходные данные и сохранить их в текстовый файл, используйте команду Command-C.

    ![Снимок экрана с выводом на консоль](media/capture-browser-trace/safari-console-select.png)

1. Упакуйте файл HAR, выходные данные консоли и запись экрана в сжатом формате, например ZIP, и предоставьте общий доступ к ним с помощью службы поддержки Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

[Обзор портала Azure](azure-portal-overview.md)