---
title: Ошибки данных журнала диагностики Azure Stream Analytics
description: В этой статье описываются различные ошибки входных и выходных данных, которые могут возникать при использовании Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0546464b4d1bcc9eaa4fbffe265486985d9c58f3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465028"
---
# <a name="azure-stream-analytics-data-errors"></a>Ошибки данных Azure Stream Analytics

Ошибки данных — это ошибки, возникающие при обработке данных.  Эти ошибки чаще всего возникают во время операций десериализации, сериализации и записи данных.  При возникновении ошибок данных Stream Analytics записывает подробные сведения и примеры событий в журналы диагностики.  В некоторых случаях сводка этих сведений также предоставляется через уведомления портала.

В этой статье описаны различные типы ошибок, причины и сведения журнала диагностики для ошибок ввода и вывода данных.

## <a name="diagnostic-log-schema"></a>Схема журнала диагностики

См. раздел [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) , чтобы просмотреть схему для журналов диагностики. Следующий код JSON представляет собой пример значения для поля **свойств** журнала диагностики для ошибки данных.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Ошибки входных данных

### <a name="inputdeserializererrorinvalidcompressiontype"></a>Инпутдесериализереррор. Инвалидкомпрессионтипе

* Причина: выбранный тип сжатия входных данных не соответствует данным.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние. сообщения с ошибками десериализации, включая недопустимый тип сжатия, удаляются из входных данных.
* Подробные сведения журнала
   * Идентификатор входного сообщения. Для концентратора событий идентификатором является PartitionId, offset и порядковый номер.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>Инпутдесериализереррор. Инвалидхеадер

* Причина: недопустимый заголовок входных данных. Например, в CSV-файле есть столбцы с повторяющимися именами.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние. сообщения с ошибками десериализации, включая недопустимый заголовок, удаляются из входных данных.
* Подробные сведения журнала
   * Идентификатор входного сообщения. 
   * Фактические данные перенимают до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>Инпутдесериализереррор. Миссингколумнс

* Причина: входные столбцы, определенные с CREATE TABLE или с помощью метки времени, не существуют.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Воздействие: события с отсутствующими столбцами удаляются из входных данных.
* Подробные сведения журнала
   * Идентификатор входного сообщения. 
   * Имена отсутствующих столбцов. 
   * Фактические данные перенимают до нескольких килобайт.

**Сообщения об ошибках**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>Инпутдесериализереррор. Типеконверсионеррор

* Причина: не удалось преобразовать входные данные в тип, указанный в операторе CREATE TABLE.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние: события с ошибкой преобразования типа удаляются из входных данных.
* Подробные сведения журнала
   * Идентификатор входного сообщения. 
   * Имя столбца и ожидаемый тип.

**Сообщения об ошибках**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>Инпутдесериализереррор. InvalidData

* Причина: входные данные имеют неправильный формат. Например, входные данные не являются допустимыми JSON.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние. обнаружены все события в сообщении после ошибки недопустимых данных, которые удаляются из входного потока.
* Подробные сведения журнала
   * Идентификатор входного сообщения. 
   * Фактические данные перенимают до нескольких килобайт.

**Сообщения об ошибках**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>инвалидинпуттиместамп

* Причина: значение выражения TIMESTAMP BY не может быть преобразовано в тип DateTime.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Воздействие: события с недопустимой входной меткой времени удаляются из входных данных.
* Подробные сведения журнала
   * Идентификатор входного сообщения. 
   * Сообщение об ошибке. 
   * Фактические данные перенимают до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>инвалидинпуттиместампкэй

* Причина: значение метки времени по СРАВНЕНию с timestampColumn имеет значение NULL.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Воздействие: события с недопустимым ключом входной метки времени удаляются из входных данных.
* Подробные сведения журнала
   * Фактический объем полезных данных до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>латеинпутевент

* Причина: разница между временем приложения и временем прибытия больше, чем окно допустимого периода поступления с задержкой.
* Предоставлено уведомление на портале: нет
* Уровень журнала диагностики: сведения
* Влияние: события позднего ввода обрабатываются в соответствии с параметром "обработать другие события" в разделе "упорядочивание событий" в конфигурации задания. Дополнительные сведения см. в разделе [политики обработки времени](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Подробные сведения журнала
   * Время приложения и время прибытия. 
   * Фактические данные перенимают до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>еарлинпутевент

* Причина: разница между временем приложения и временем прибытия превышает 5 минут.
* Предоставлено уведомление на портале: нет
* Уровень журнала диагностики: сведения
* Влияние. ранние события ввода обрабатываются в соответствии с параметром "обработать другие события" в разделе "упорядочивание событий" в конфигурации задания. Дополнительные сведения см. в разделе [политики обработки времени](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Подробные сведения журнала
   * Время приложения и время прибытия. 
   * Фактические данные перенимают до нескольких килобайт.

**Сообщение об ошибке**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>аутофордеревент

* Причина: событие считается неупорядоченным в соответствии с определенным окном допустимости неупорядоченности.
* Предоставлено уведомление на портале: нет
* Уровень журнала диагностики: сведения
* Влияние: неупорядоченные события обрабатываются в соответствии с параметром "обработать другие события" в разделе "упорядочивание событий" в конфигурации задания. Дополнительные сведения см. в разделе [политики обработки времени](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Подробные сведения журнала
   * Фактические данные перенимают до нескольких килобайт.

**Сообщение об ошибке**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Ошибки выходных данных

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>Аутпутдатаконверсионеррор. Рекуиредколумнмиссинг

* Причина: столбец, необходимый для вывода, не существует. Например, существует столбец, определенный как таблица Azure PartitionKey не.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние. все ошибки преобразования выходных данных, включая отсутствующий обязательный столбец, обрабатываются в соответствии с параметром [политики выходных данных](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Подробные сведения журнала
   * Имя столбца и либо идентификатор записи, либо часть записи.

**Сообщение об ошибке**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>Аутпутдатаконверсионеррор. Колумннамеинвалид

* Причина: значение столбца не соответствует выходным данным. Например, имя столбца не является допустимым столбцом таблицы Azure.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние. все ошибки преобразования выходных данных, включая недопустимое имя столбца, обрабатываются в соответствии с параметром [политики выходных данных](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Подробные сведения журнала
   * Имя столбца и либо идентификатор записи, либо часть записи.

**Сообщение об ошибке**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>Аутпутдатаконверсионеррор. Типеконверсионеррор

* Причина: столбец не может быть преобразован к допустимому типу в выходных данных. Например, значение Column несовместимо с ограничениями или типами, определенными в таблице SQL.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние. все ошибки преобразования выходных данных, включая ошибки преобразования типов, обрабатываются в соответствии с параметром [политики выходных данных](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Подробные сведения журнала
   * Имя столбца.
   * Идентификатор записи или часть записи.

**Сообщение об ошибке**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>Аутпутдатаконверсионеррор. Рекордексцеедедсизелимит

* Причина: значение сообщения больше, чем поддерживаемый размер выходных данных. Например, для выходных данных концентратора событий запись имеет больший размер, чем 1 МБ.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние. все ошибки преобразования выходных данных, включая предельный размер записи, обрабатываются в соответствии с параметром [политики выходных данных](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Подробные сведения журнала
   * Идентификатор записи или часть записи.

**Сообщение об ошибке**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>Аутпутдатаконверсионеррор. Дупликатекэй

* Причина: запись уже содержит столбец с тем же именем, что и системный столбец. Например, CosmosDB выходные данные со столбцом с именем ID, если столбец ID относится к другому столбцу.
* Предоставленное уведомление портала: Да
* Уровень журнала диагностики: предупреждение
* Влияние. все ошибки преобразования выходных данных, включая дублирующиеся ключи, обрабатываются в соответствии с параметром [политики выходных данных](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Подробные сведения журнала
   * Имя столбца.
   * Идентификатор записи или часть записи.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](stream-analytics-job-diagnostic-logs.md)

* [Общие сведения о мониторинге заданий Stream Analytics и мониторинге запросов](stream-analytics-monitoring.md)
