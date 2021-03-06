---
title: Подключение Excel к Apache Hadoop с помощью Power Query — Azure HDInsight
description: Узнайте, как пользоваться преимуществами компонентов бизнес-аналитики и применять Power Query для Excel для получения доступа к данным, хранящимся в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435808"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Подключение Excel к Apache Hadoop с помощью Power Query

Одной из ключевых особенностей решения Майкрософт для работы с большими данными является интеграция компонентов бизнес-аналитики Майкрософт с кластерами Apache Hadoop в службе Azure HDInsight. Важнейшим примером является возможность подключения Excel к учетной записи хранения Azure, в которой хранятся данные, связанные с кластером Hadoop, с помощью надстройки Microsoft Power Query для Excel. В этой статье приводится пошаговое руководство по настройке и использованию Power Query для запроса данных, связанных с кластером Hadoop, который управляется с помощью HDInsight.

## <a name="prerequisites"></a>Технические условия

* Кластер Apache Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).
* Рабочая станция под управлением Windows 10, 7, Windows Server 2008 R2 или более поздней операционной системы.
* Office 2016, Office 2013 профессиональный плюс, Office 365 ProPlus, Excel 2013 standalone или Office 2010 профессиональный плюс.

## <a name="install-microsoft-power-query"></a>Установка Microsoft Power Query

Power Query может импортировать данные, которые были выведены или созданы заданием Hadoop, выполняющимся в кластере HDInsight.

В Excel 2016 надстройка Power Query находится на вкладке "Данные" ленты в группе "Скачать & преобразовать". Для более старых версий Excel Скачайте Microsoft Power Query для Excel из [центра загрузки Майкрософт](https://go.microsoft.com/fwlink/?LinkID=286689) и установите его.

## <a name="import-hdinsight-data-into-excel"></a>Импорт данных HDInsight в Excel

Надстройка Power Query для Excel удобна для импорта данных из кластера HDInsight в Excel, где можно использовать средства бизнес-аналитики, такие как PowerPivot и Power Map, для изучения, анализа и представления данных.

1. Запустите Excel.

1. Создайте новую пустую книгу.

1. Выполните указанные ниже действия для вашей версии Excel.

   * Excel 2016

     * Выберите > **данных** > **получения** > данных **из** Azure > **из Azure HDInsight (HDFS)** .

       ![HDi. PowerQuery. Селексдисаурце. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013 или 2010

     * Выберите **Power Query** > **из** > Azure **из Microsoft Azure HDInsight**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Примечание.** Если меню **Power Query** не отображается, последовательно выберите пункты **файл** > **Параметры** > **надстройки**и **надстройки COM** в раскрывающемся списке **управления** в нижней части страницы. Нажмите кнопку **Перейти...** и убедитесь, что установлен флажок «Power Query для Excel».

       **Примечание.** Power Query также позволяет импортировать данные из HDFS путем выбора **из других источников**.

1. В диалоговом окне **Azure HDInsight (HDFS)** в текстовом поле **имя учетной записи или URL-адрес** введите имя учетной записи хранилища больших двоичных объектов Azure, связанной с кластером. Нажмите кнопку **ОК**. Эта учетная запись может быть учетной записью хранения по умолчанию или связанной учетной записью хранения.  Формат — `https://StorageAccountName.blob.core.windows.net/`.

1. В поле **ключ учетной записи**введите ключ для учетной записи хранения BLOB-объектов и нажмите кнопку **подключить**. (Вводить данные учетной записи требуется только при первом доступе к этому магазину.)

1. В области **Навигатор** слева от редактора запросов дважды щелкните имя контейнера хранилища BLOB-объектов, связанного с кластером. По умолчанию имя контейнера совпадает с именем кластера.

1. В столбце **имя** поиска **HiveSampleData. txt** (путь к папке — **.. /Хиве/варехаусе/хивесамплетабле/** ), а затем выберите **двоичный файл** слева от HiveSampleData. txt. HiveSampleData.txt поставляется вместе с кластером. При необходимости можно использовать собственный файл.

    ![Импорт данных Power Query HDI в Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Если необходимо, можно переименовать имена столбцов. Когда будете готовы, нажмите кнопку **закрыть & загрузить**.  Данные загружены в книгу.

    ![HDI Excel Power Query Imported Table](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Дальнейшие действия

В этой статье было показано, как использовать Power Query для извлечения данных из HDInsight в Excel. Аналогичным образом можно извлекать данные из HDInsight в базу данных SQL Azure. Также можно передать данные в HDInsight. Дополнительные сведения см. в следующих статьях:

* [Визуализация данных Apache Hive с помощью Microsoft Power BI в Azure HDInsight](apache-hadoop-connect-hive-power-bi.md)
* [Visualize Interactive Query Hive data with Microsoft Power BI using DirectQuery in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive из кластера Interactive Query с помощью Microsoft Power BI и DirectQuery в Azure HDInsight).
* [Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
