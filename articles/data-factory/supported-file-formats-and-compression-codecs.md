---
title: Поддерживаемые форматы файлов в фабрике данных Azure
description: В этой статье описаны форматы файлов и коды сжатия, поддерживаемые соединителями на основе файлов в фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439574"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Поддерживаемые форматы файлов и кодеки сжатия в фабрике данных Azure

*Эта статья относится к следующим соединителям: [Amazon S3](connector-amazon-simple-storage-service.md), [большой двоичный объект Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md), [хранилище файлов Azure](connector-azure-file-storage.md), [Файловая система](connector-file-system.md), [FTP](connector-ftp.md), [облачное хранилище](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)и [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

[Действие копирования](copy-activity-overview.md) можно использовать для копирования файлов "как есть" между двумя хранилищами данных на основе файлов. в этом случае данные копируются эффективно без сериализации или десериализации. 

Кроме того, можно выполнять синтаксический анализ или создание файлов определенного формата. Например, можно выполнить следующие действия.

* Копирование данных из локальной базы данных SQL Server и запись в Azure Data Lake Storage 2-го поколения в формате Parquet.
* Копирование файлов в текстовом формате (CSV) из локальной файловой системы и запись в хранилище BLOB-объектов Azure в формате Avro.
* Скопируйте ZIP-файлы из локальной файловой системы, распакуйте их в оперативном режиме и запишите извлеченные файлы в Azure Data Lake Storage 2-го поколения.
* Копирование данных в формате сжатого текста gzip (CSV) из хранилища BLOB-объектов Azure и их запись в базу данных SQL Azure.
* Многие другие действия, требующие сериализации, десериализации или сжатия и распаковки.

## <a name="next-steps"></a>Дальнейшие действия

См. другие статьи о действиях копирования:

- [Действие копирования в фабрике данных Azure](copy-activity-overview.md)
- [Руководство по настройке производительности действия копирования](copy-activity-performance.md)
