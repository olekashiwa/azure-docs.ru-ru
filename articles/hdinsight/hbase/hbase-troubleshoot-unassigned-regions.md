---
title: Проблемы с серверами регионов в Azure HDInsight
description: Проблемы с серверами регионов в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887144"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Проблемы с серверами регионов в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Сценарий: неназначенные регионы

### <a name="issue"></a>Проблема

При выполнении команды `hbase hbck` отображается сообщение об ошибке следующего вида:

```
multiple regions being unassigned or holes in the chain of regions
```

В пользовательском интерфейсе Apache HBase Master можно увидеть число регионов, которые не сбалансированы по всем серверам регионов. Затем можно выполнить команду `hbase hbck`, чтобы увидеть пропуски в цепочке регионов.

### <a name="cause"></a>Причина

Отверстия могут быть результатом автономных регионов.

### <a name="resolution"></a>Разрешение

Исправьте назначения. Выполните следующие действия, чтобы вернуть неназначенные регионы в нормальное состояние.

1. Войдите в кластер HDInsight HBase с помощью SSH.

1. Выполните команду `hbase zkcli`, чтобы подключиться с помощью оболочки ZooKeeper.

1. Выполните команду `rmr /hbase/regions-in-transition` или `rmr /hbase-unsecure/regions-in-transition`.

1. Выйдите из оболочки Zookeeper с помощью команды `exit`.

1. Откройте пользовательский интерфейс Ambari Apache и перезапустите службу активного главного узла HBase.

1. Выполните команду `hbase hbck` еще раз (без дополнительных параметров). Проверьте выходные данные и убедитесь, что все регионы назначены.

---

## <a name="scenario-dead-region-servers"></a>Сценарий: неработающие серверы регионов

### <a name="issue"></a>Проблема

Не удается запустить серверы регионов.

### <a name="cause"></a>Причина

Множественное разделение каталогов WAL.

1. Возвращает список текущих Wal: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Проверьте файл `wals.out`. Если слишком много разделенных каталогов (начиная с *-разделение), возможно, сервер регионов не работает из-за этих каталогов.

### <a name="resolution"></a>Разрешение

1. Отключите HBase на портале Ambari.

1. Выполните `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`, чтобы получить актуальный список Wal.

1. Переместите *-разделение каталогов во временную папку, `splitWAL`и удалите *-разделение каталогов.

1. Выполните команду `hbase zkcli`, чтобы подключиться с помощью оболочки Zookeeper.

1. Выполните процедуру `rmr /hbase-unsecure/splitWAL`.

1. Перезапустите службу HBase.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
