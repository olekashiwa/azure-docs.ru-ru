---
title: Ограничения памяти и параллелизма
description: Из этой статьи вы узнаете об ограничениях памяти и параллелизма, существующих для различных уровней производительности и классов ресурсов в хранилище данных SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 12/04/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: dfdaef0002f068dc4c9044e979b169de779cf6d5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851287"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Ограничения параллелизма и памяти для хранилища данных SQL Azure
Из этой статьи вы узнаете об ограничениях памяти и параллелизма, существующих для различных уровней производительности и классов ресурсов в хранилище данных SQL Azure.  

## <a name="data-warehouse-capacity-settings"></a>Параметры емкости хранилища данных
В таблицах ниже приведена максимальная емкость хранилища данных для разных уровней производительности. Сведения о том, как изменить уровень производительности, см. в статье [Краткое руководство. Масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью портала Azure](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Уровни обслуживания

Уровни обслуживания находятся в диапазоне от DW100c до DW30000c.

| Уровень производительности | Вычислительные узлы | Распределения на один вычислительный узел | Объем памяти на одно хранилище данных (ГБ) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Максимальный уровень обслуживания — DW30000c, который содержит 60 Вычисленийных узлов и одно распределение на один расчетный узел. Например, хранилище данных объемом 600 ТБ на уровне DW30000c обрабатывает приблизительно 10 ТБ на один узел вычислений.

## <a name="concurrency-maximums-for-workload-groups"></a>Максимум параллелизма для групп рабочей нагрузки
С появлением [групп рабочей нагрузки](sql-data-warehouse-workload-isolation.md)понятие слотов выдачи больше не применяется.  Ресурсы на запрос выделяются в процентах и задаются в определении группы рабочей нагрузки.  Однако даже при удалении слотов выдачи для запросов требуется минимальный объем ресурсов на основе уровня обслуживания.  В приведенной ниже таблице определен минимальный объем ресурсов, необходимый для каждого запроса между уровнями обслуживания и связанными параллелизмом, которые можно достичь. 

|Уровень обслуживания|Максимальное число одновременных запросов|Мин% поддерживается для REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25 %|
|DW200c|8|12,5 %|
|DW300c|12|8 %|
|DW400c|16|6,25 %|
|DW500c|20|5 %|
|DW1000c|32|3 %|
|DW1500c|32|3 %|
|DW2000c|48|2 %|
|DW2500c|48|2 %|
|DW3000c|64|1,5 %|
|DW5000c|64|1,5 %|
|DW6000c|128|0.75%|
|DW7500c|128|0.75%|
|DW10000c|128|0.75%|
|DW15000c|128|0.75%|
|DW30000c|128|0.75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Максимальные значения параллелизма для классов ресурсов
Чтобы каждый запрос имел достаточно ресурсов для эффективной работы, хранилище данных SQL отслеживает использование ресурсов путем присвоения каждому запросу определенного количества слотов параллельности. Система помещает запросы в очередь на основе важности и слотов выдачи. Запросы ожидают в очереди, пока не будет доступно достаточное число слотов. Слоты [важности](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) и параллелизма ОПРЕДЕЛЯЮТ приоритеты ЦП. Дополнительные сведения см. в статье [Анализ рабочей нагрузки](analyze-your-workload.md).

**Классы статических ресурсов**

В следующей таблице показано максимальное число одновременных запросов и слотов выдачи для каждого [класса статических ресурсов](resource-classes-for-workload-management.md).  

| Уровень обслуживания | Максимальное число одновременных запросов | Доступные слоты выдачи | Слоты, используемые staticrc10 | Слоты, используемые staticrc20 | Слоты, используемые staticrc30 | Слоты, используемые staticrc40 | Слоты, используемые staticrc50 | Слоты, используемые staticrc60 | Слоты, используемые staticrc70 | Слоты, используемые staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Классы динамических ресурсов**

В следующей таблице показано максимальное число одновременных запросов и слотов выдачи для каждого [класса динамических ресурсов](resource-classes-for-workload-management.md). Динамические классы ресурсов используют выделение памяти в процентах 3-10-22-70 для классов ресурсов малых и средних объемов ксларже на всех уровнях обслуживания.

| Уровень обслуживания | Максимальное число одновременных запросов | Доступные слоты выдачи | Слоты, используемые smallrc | Слоты, используемые mediumrc | Слоты, используемые largerc | Слоты, используемые xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |


Если нет достаточных свободных слотов для выполнения запроса, запросы помещаются в очередь и выполняются в зависимости от важности.  При наличии эквивалентной важности запросы выполняются по принципу «первым поверх».  После завершения запросов, а также после того, как число запросов и слотов падает ниже ограничения, хранилище данных SQL освобождает запросы в очереди. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о том, как использовать классы ресурсов для оптимизации рабочей нагрузки, см. в следующих статьях:
* [Классы ресурсов для управления рабочими нагрузками](resource-classes-for-workload-management.md)
* [Анализ рабочей нагрузки](analyze-your-workload.md)