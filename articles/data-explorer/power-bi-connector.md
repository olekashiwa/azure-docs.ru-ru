---
title: Визуализация данных с помощью соединителя Azure Data Explorer для Power BI
description: 'В этой статье вы узнаете, как использовать один из трех вариантов визуализации данных в Power BI: соединитель Power BI для Azure обозреватель данных.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 361ea6ed76207e8e9721f64df61738b6cd9631dc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024216"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Визуализация данных с помощью соединителя Azure Data Explorer для Power BI

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Power BI — это решение бизнес-аналитики, позволяющее визуализировать данные и делиться результатами с коллегами. Azure Data Explorer поддерживает три варианта подключения к данным в Power BI: использование встроенного соединителя, импорт запроса из Azure Data Explorer или использование SQL-запроса. В этой статье показано, как использовать встроенный соединитель для получения данных и визуализации их в Power BI отчете. Использование собственного соединителя Azure обозреватель данных для создания панелей мониторинга Power BI — это просто. Соединитель Power BI поддерживает [режимы подключения "Импорт" и "прямой запрос](https://docs.microsoft.com/power-bi/desktop-directquery-about)". Вы можете создавать панели мониторинга с помощью **импорта** или режима **DirectQuery** в зависимости от сценария, масштаба и требований к производительности. 

## <a name="prerequisites"></a>предварительным требованиям

Для выполнения этой статьи необходимо следующее:

* Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.
* учетная запись электронной почты организации, подключенной к Azure Active Directory, для доступа к [кластеру help в Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples);
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (выберите **Скачать бесплатно**);

## <a name="get-data-from-azure-data-explorer"></a>Получение данных из Azure Data Explorer

Сначала подключитесь к кластеру help Azure Data Explorer, а затем выведите часть данных из таблицы *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. В Power BI Desktop на вкладке **Главная** выберите **Получение данных**, а затем **Дополнительно**.

    ![Получение данных](media/power-bi-connector/get-data-more.png)

1. Выполните поиск *Обозреватель данных Azure*, выберите **Azure обозреватель данных** затем **Подключиться**.

    ![Поиск и получение данных](media/power-bi-connector/search-get-data.png)

1. На экране **Обозреватель данных Azure (Kusto)** заполните форму следующими сведениями.

    ![Кластер, база данных, параметры таблицы](media/power-bi-connector/cluster-database-table.png)

    **Параметр** | **Значение** | **Описание поля**
    |---|---|---|
    | HDInsight | *https://help.kusto.windows.net* | URL-адрес кластера help. Для других кластеров URL-адрес имеет вид *https://\<Имя_кластера\>.\<Регион\>.kusto.windows.net*. |
    | База данных | Не указывайте | База данных, размещенная в кластере, к которому вы подключаетесь. Это будет указано позже. |
    | Имя таблицы | Не указывайте | Одна из таблиц в базе данных или запрос, например <code>StormEvents \| take 1000</code>. Это будет указано позже. |
    | Расширенные параметры | Не указывайте | Варианты запроса, например размер результирующего набора. |
    | Режим подключения к данным | *DirectQuery* | Определяет, должно ли решение Power BI импортировать данные или подключаться непосредственно к источнику данных. С этим соединителем можно использовать любой вариант. |
    | | | |
    
    > [!NOTE]
    > В режиме **импорта** данные перемещаются в Power BI. В режиме **DirectQuery** данные запрашиваются непосредственно из кластера Azure обозреватель данных.
    >
    > Используйте режим **импорта** в следующих случаях:
    > * Ваш набор данных невелик.
    > * Данные практически в реальном времени не нужны. 
    > * Данные уже агрегированы или вы выполняете [агрегирование в Kusto](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > Используйте режим **DirectQuery** в следующих случаях:
    > * Ваш набор данных очень большой. 
    > * Вам потребуются данные практически в реальном времени.   

1. Если вы еще не установили подключение к кластеру help, выполните вход. Войдите с помощью учетной записи организации и выберите **Подключение**.

    ![входа](media/power-bi-connector/sign-in.png)

1. На экране **Навигатор** разверните базу данных **Примеры**, выберите **StormEvents**, а затем **Изменить**.

    ![Выбор таблицы](media/power-bi-connector/select-table.png)

    Таблица откроется в редакторе запросов Power, где можно изменить строки и столбцы перед импортом данных.

1. В редакторе Power Query щелкните стрелку рядом со столбцом **DamageCrops** и выберите **Сортировка по убыванию**.

    ![Сортировка по убыванию для DamageCrops](media/power-bi-connector/sort-descending.png)

1. На вкладке **Главная** выберите **Сохранить строки**, а затем **Сохранить верхние строки**. Введите значение *1000* для сохранения 1000 верхних строк отсортированной таблицы.

    ![Сохранение верхних строк](media/power-bi-connector/keep-top-rows.png)

1. На вкладке **Главная** выберите **Закрыть и применить**.

    ![Закрыть и применить](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Визуализация данных в отчете

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если отчет, созданный для этой статьи, больше не нужен, удалите Power BI Desktop (pbix-файл).

## <a name="next-steps"></a>Дополнительная информация

[Советы по использованию соединителя Azure обозреватель данных для Power BI запроса данных](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
