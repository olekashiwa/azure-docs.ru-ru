---
title: Резервное копирование & репликацию для Apache HBase, Phoenix — Azure HDInsight
description: Настройка резервного копирования и репликации для Apache HBase и Apache Phoenix в Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: c6d33158b581bf4394a0d1bac2b277830328e110
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495937"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Настройка резервного копирования и репликации Apache HBase и Apache Phoenix в HDInsight

Apache HBase поддерживает несколько способов защиты от потери данных:

* копирование папки `hbase`;
* Экспорт и импорт
* Копирование таблиц
* Моментальные снимки
* Репликация

> [!NOTE]  
> Apache Phoenix хранит метаданные в таблицах HBase. За счет этого при резервном копировании таблиц системного каталога HBase также создаются резервные копии метаданных.

В разделах ниже описаны сценарии использования каждого из этих подходов.

## <a name="copy-the-hbase-folder"></a>Копирование папки hbase

При использовании этого подхода копируются все данные HBase. В этом случае выбирать определенные подмножества семейств столбцов или таблиц нельзя. Последующие подходы предоставляют более широкие возможности управления.

HBase в HDInsight использует хранилище по умолчанию, выбранное при создании кластера. Это могут быть большие двоичные объекты службы хранилища Azure или Azure Data Lake Storage. В любом случае HBase хранит файлы данных и метаданных по следующему пути:

    /hbase

* В учетной записи службы хранилища Azure папка `hbase` находится в корне контейнера больших двоичных объектов:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* В Azure Data Lake Storage папка `hbase` находится под корневым путем, указанным при подготовке кластера. Обычно этот путь к корневому каталогу имеет папку `clusters` с вложенной папкой, имя которой совпадает с кластером HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

В любом случае папка `hbase` содержит все данные, записываемые HBase на диск. Однако в ней могут отсутствовать данные, размещенные в памяти. Чтобы можно было полагаться на эту папку как на точное представление данных HBase, необходимо завершить работу кластера.

После удаления кластера данные можно оставить в стандартном расположении или скопировать в новое:

* Создайте экземпляр HDInsight, указывающий на текущее место хранения. Созданный экземпляр будет содержать все имеющиеся данные.

* Скопируйте папку `hbase` в другой контейнер больших двоичных объектов службы хранилища Azure или другое расположение Data Lake Storage, а затем запустите новый кластер с этими данными. Для службы хранилища Azure используйте служебную программу [AzCopy](../../storage/common/storage-use-azcopy.md), а для Data Lake Storage — [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Экспорт и импорт

В исходном кластере HDInsight используйте [программу экспорта](https://hbase.apache.org/book.html#export) (входит в состав HBase) для экспорта данных из исходной таблицы в подключенное по умолчанию хранилище. Затем экспортированную папку можно скопировать в целевое место хранения и запустить [программу импорта](https://hbase.apache.org/book.html#import) в целевом кластере HDInsight.

Чтобы экспортировать данные таблицы, сначала подключитесь к головному узлу исходного кластера HDInsight, а затем выполните следующую команду `hbase`:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Каталог экспорта уже не должен существовать. В имени таблицы учитывается регистр.

Чтобы импортировать табличные данные, подключитесь к головному узлу целевого кластера HDInsight по протоколу SSH и выполните следующую команду `hbase`:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Таблица уже должна существовать.

Укажите полный путь экспорта в хранилище по умолчанию или любое подключенное хранилище. Например, в службе хранилища Azure:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

В Azure Data Lake Storage 2-го поколения используется следующий синтаксис.

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

В Azure Data Lake Storage 1-го поколения используется следующий синтаксис.

    adl://<accountName>.azuredatalakestore.net:443/<path>

Этот подход обеспечивает степень детализации на уровне таблицы. Кроме того, вы можете указать диапазон дат строк, которые нужно включить. Это позволит выполнить процесс пошагово. Каждая дата указывается в миллисекундах с момента начала эпохи Unix.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Обратите внимание, что нужно указать количество версий каждой экспортируемой строки. Чтобы включить все версии в диапазон дат, задайте параметру `<numberOfVersions>` значение, превышающее максимально возможное число версий строк, например 100 000.

## <a name="copy-tables"></a>Копирование таблиц

[Служебная программа CopyTable](https://hbase.apache.org/book.html#copy.table) копирует данные из исходной таблицы по строке в существующую целевую таблицу с той же схемой, что и источник. Целевая таблица может находиться в том же или другом кластере HBase. В именах таблиц учитывается регистр.

Чтобы использовать CopyTable в кластере, подключитесь к головному узлу исходного кластера HDInsight по протоколу SSH, а затем выполните следующую команду `hbase`:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>


Чтобы скопировать таблицу в другой кластер с помощью CopyTable, добавьте параметр `peer` с адресом целевого кластера:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Адрес назначения состоит из следующих трех частей:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` — это список узлов Apache ZooKeeper, разделенный запятыми, например:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* По умолчанию параметр `<Port>` в HDInsight имеет значение 2181, а `<ZnodeParent>` — `/hbase-unsecure`. Поэтому полный параметр `<destinationAddress>` выглядит так:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Сведения о том, как получить эти значения кластера HDInsight, см. в разделе [Сбор списка кворума Apache ZooKeeper вручную](#manually-collect-the-apache-zookeeper-quorum-list).

Служебная программа CopyTable также поддерживает параметры, которые позволяют указать диапазон времени копируемых строк и подмножество семейств столбцов в копируемой таблице. Чтобы просмотреть полный список поддерживаемых параметров, запустите CopyTable без параметров:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable сканирует все содержимое исходной таблицы, которое нужно скопировать в целевую таблицу. Это может привести к снижению производительности кластера HBase.

> [!NOTE]  
> Автоматизировать копирование данных между таблицами можно с помощью скрипта `hdi_copy_table.sh` в репозитории со [служебными программами HBase Azure](https://github.com/Azure/hbase-utils/tree/master/replication) в GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Сбор списка кворума Apache ZooKeeper вручную

Когда оба кластера HDInsight находятся в той же виртуальной сети, как описано выше, разрешение имен внутренних узлов выполняется автоматически. Чтобы использовать CopyTable для кластеров HDInsight в двух отдельных виртуальных сетях, подключенных VPN-шлюзом, необходимо предоставить IP-адреса узлов Zookeeper в кворуме.

Чтобы получить имена узлов кворума, выполните следующую команду cURL:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Эта команда cURL извлекает документ JSON с информацией о конфигурации HBase, а команда grep возвращает только запись hbase.zookeeper.quorum, например:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Значение имен узлов кворума — это вся строка после двоеточия.

Чтобы получить IP-адреса этих узлов, выполните следующую команду cURL в каждом узле из предыдущего списка:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

В этой команде cURL `<zookeeperHostFullName>` —это полное DNS-имя узла ZooKeeper, как показано в этом примере: `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Выходные данные этой команды содержат IP-адрес указанного узла, например:

    100    "ip" : "10.0.0.9",

Собрав IP-адреса всех узлов ZooKeeper в кворуме, перестройте целевой адрес:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

Пример.

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Моментальные снимки

[Моментальные снимки](https://hbase.apache.org/book.html#ops.snapshots) позволяют создавать резервные копии данных в хранилище HBase на момент времени. Они имеют минимальные издержки и создаются в течение нескольких секунд. Во время этой операции, которая, по сути, является операцией с метаданными, записываются имена всех файлов, сохраненных на этот момент в хранилище. При создании моментального снимка фактические данные не копируются. Моментальные снимки зависят от постоянных данных, хранящихся в HDFS, где операции обновления, удаления и вставки представляют собой новые данные. Вы можете восстановить (*клонировать*) моментальный снимок в том же кластере или экспортировать его в другой кластер.

Чтобы создать моментальный снимок, подключитесь к головному узлу кластера HDInsight HBase по протоколу SSH и запустите оболочку `hbase`:

    hbase shell

В оболочке hbase используйте команду создания моментального снимка, указав имена таблицы и этого моментального снимка:

    snapshot '<tableName>', '<snapshotName>'

Чтобы в оболочке `hbase` восстановить моментальный снимок по имени, сначала отключите таблицу, а затем восстановите моментальный снимок и повторно включите таблицу:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Чтобы восстановить моментальный снимок в новую таблицу, используйте команду clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Чтобы экспортировать моментальный снимок в HDFS, где его будет использовать другой кластер, сначала создайте моментальный снимок, как описано выше, и воспользуйтесь служебной программой ExportSnapshot. Запустите эту программу из сеанса SSH на головном узле, а не в оболочке `hbase`:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

В качестве значения параметра `<hdfsHBaseLocation>` можно указать любое место хранения, поддерживаемое исходным кластером. Кроме того, это значение должно указывать на папку hbase, используемую целевым кластером. Например, если к исходному кластеру подключена дополнительная учетная запись службы хранилища Azure и эта учетная запись предоставляет доступ к контейнеру, используемому по умолчанию хранилищем целевого кластера, используйте следующую команду:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

После экспорта моментального снимка подключитесь к головному узлу целевого кластера по протоколу SSH и восстановите этот моментальный снимок с помощью команды restore_snapshot, как описано выше.

Моментальные снимки предоставляют полную резервную копию таблицы на момент выполнения команды `snapshot`. Моментальные снимки не предоставляют возможность выполнять добавочные моментальные снимки по времени Windows, а также указывать подмножества семейств столбцов для включения в моментальный снимок.

## <a name="replication"></a>Репликация

[Репликация HBase](https://hbase.apache.org/book.html#_cluster_replication) автоматически передает транзакции из исходного кластера в целевой кластер, используя асинхронный механизм с минимальными издержками в исходном кластере. В HDInsight репликацию можно настроить между кластерами, где:

* исходный и целевой кластеры расположены в одной виртуальной сети;
* исходный и целевой кластеры расположены в разных виртуальных сетях, соединенных VPN-шлюзом, но при этом находятся в одном географическом расположении;
* исходный и целевой кластеры расположены в разных виртуальных сетях, соединенных VPN-шлюзом, и находятся в разных географических расположениях.

Вот основные шаги по настройке репликации:

1. В исходном кластере создайте таблицы и заполните их данными.
2. В целевом кластере создайте пустые целевые таблицы со схемой исходной таблицы.
3. Зарегистрируйте целевой кластер как кэширующий узел в исходном кластере.
4. Включите репликацию в нужных исходных таблицах.
5. Скопируйте имеющиеся данные из исходных таблиц в целевые.
6. Репликация автоматически копирует новые измененные данные в исходные таблицы в целевых таблицах.

Чтобы включить репликацию в HDInsight, примените действие скрипта к выполняющемуся исходному кластеру HDInsight. Пошаговые инструкции по включению репликации в кластере или настройке репликации в образцах кластеров, созданных в виртуальных сетях с помощью шаблонов Azure Resource Manager, см. в статье о [Настройке репликации Apache HBase](apache-hbase-replication.md). Эта статья также содержит инструкции по включению репликации метаданных Phoenix.

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка репликации Apache HBase](apache-hbase-replication.md)
* [Работа с программой импорта и экспорта HBase](https://blogs.msdn.microsoft.com/data_otaku/2016/12/21/working-with-the-hbase-import-and-export-utility/)
