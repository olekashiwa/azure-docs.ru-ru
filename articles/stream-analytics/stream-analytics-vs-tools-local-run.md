---
title: Тестирование Azure Stream Analytics запросов локально в Visual Studio
description: В этой статье описывается, как локально протестировать запросы с помощью инструментов Azure Stream Analytics для Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 34c8555356d5c0142d7b677c8119fe66806ab064
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834915"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Локальное тестирование запросов Stream Analytics с помощью Visual Studio

Azure Stream Analytics инструменты для Visual Studio можно использовать для локального тестирования заданий Stream Analytics с помощью демонстрационных данных или [динамических данных](stream-analytics-live-data-local-testing.md). 

В этом [кратком руководстве](stream-analytics-quick-create-vs.md) вы узнаете, как создать задание Stream Analytics с помощью Visual Studio.

## <a name="test-your-query"></a>Тестирование запроса

В проекте Azure Stream Analytics дважды щелкните **Script.asaql**, чтобы открыть этот скрипт в редакторе. Вы можете предварительно скомпилировать запрос, чтобы увидеть все синтаксические ошибки. Редактор запросов поддерживает технологию IntelliSense, выделение синтаксиса цветом и маркер ошибок.

![Редактор запросов](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Добавление локальных входных данных

Для проверки запроса по локальным статическим данным щелкните правой кнопкой мыши входные данные и выберите функцию **Добавить локальный ввод**.
   
![Добавление локальных входных данных](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Во всплывающем окне выберите демонстрационные данные из локальной папки и нажмите кнопку **Сохранить**
   
![Добавление локальных входных данных](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Файл **local_EntryStream.json** будет автоматически добавлен в папку входных данных.
   
![Список файлов в локальной папке входных данных](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
В редакторе запросов щелкните **Запустить локально**. Или можно нажать клавишу F5.
   
![Локальный запуск](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Выходные данные можно просмотреть в виде таблицы непосредственно из Visual Studio.

![Выходные данные в виде таблицы](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Путь к выходным данным можно найти в выходных данных консоли. Нажмите любую клавишу, чтобы открыть папку результатов.
   
![Локальный запуск](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Проверьте результаты в локальной папке.
   
![Результат в локальной папке](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Пример ввода
Вы также можете применить выборку примеров входных данных из источников к локальному файлу. Щелкните правой кнопкой мыши файл конфигурации входных данных и выберите **Образцы данных**. 

![Образец данных](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Вы можете только распределить поток данных из Центров событий или Центров Интернета вещей. Другие источники входных данных не поддерживаются. Во всплывающем диалоговом окне введите локальный путь для сохранения демонстрационных данных и нажмите кнопку **Образец**.

![Конфигурация примера данных](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Ход выполнения можно просмотреть в окне **вывода**. 

![Выходные данные для примера данных](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Создание задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md)
* [Просмотр заданий Azure Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools.md)
* [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md)
* [Руководство. Развертывание заданий Azure Stream Analytics с помощью CI/CD и Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Непрерывная интеграция и разработка с помощью инструментов Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
