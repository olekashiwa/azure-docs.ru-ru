---
title: Проверка ошибок заданий и задач в пакетной службе Azure | Документация Майкрософт
description: Ошибки для проверки и устранения неполадок заданий и задач
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 12/01/2019
ms.author: markscu
ms.openlocfilehash: c4e36d76bf85b9715a817dbeb7c690aa77f8d978
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852189"
---
# <a name="job-and-task-error-checking"></a>Проверка ошибок заданий и задач

Существуют различные ошибки, которые могут возникнуть при добавлении заданий и задач. Обнаружение сбоев для этих операций является простым, так как все сбои возвращаются API, интерфейсом командной строки или интерфейсом пользователя.  Однако существуют сбои, которые могут произойти позже, когда задания и задачи планируются и выполняются.

В этой статье рассматриваются ошибки, которые могут возникнуть после отправки заданий и задач. В нем перечисляются и объясняются ошибки, которые необходимо проверить и обработать.

## <a name="jobs"></a>Задания

Задание — это группирование одной или нескольких задач. задачи фактически указывают командные строки для выполнения.

При добавлении задания можно указать следующие параметры, которые могут влиять на то, как может произойти сбой задания.

- [Ограничения заданий](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - При необходимости можно указать свойство `maxWallClockTime`, чтобы задать максимальное время, в течение которого задание может быть активным или работающим. Если превышено, задание будет завершено со свойством `terminateReason`, заданным в [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) для задания.
- [Задача подготовки задания](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Если указано, задача подготовки задания выполняется при первом запуске задачи на узле. Задача подготовки задания может завершиться ошибкой, что приведет к невыполнению задачи, а задание не будет завершено.
- [Задача выпуска задания](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Задачу снятия задания можно указать только в том случае, если настроена задача подготовки задания. При завершении задания задание выпуска задания выполняется на каждом узле пула, где выполнялась задача подготовки задания. Задача выпуска задания может завершиться ошибкой, но задание по-прежнему будет переноситься в состояние `completed`.

### <a name="job-properties"></a>Свойства задания

Следующие свойства задания должны проверяться на наличие ошибок:

- "[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)":
  - Свойство `terminateReason` может иметь значения, указывающие, что `maxWallClockTime`, указанные в ограничениях заданий, были превышены и поэтому задание было завершено. Его также можно задать, чтобы указать, что задача завершилась ошибкой, если свойство `onTaskFailure` задания было настроено соответствующим образом.
  - Свойство [счедулинжеррор](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) устанавливается при возникновении ошибки планирования.
 
### <a name="job-preparation-tasks"></a>Задачи подготовки задания

Если для задания задана задача подготовки задания, то экземпляр этой задачи будет выполняться при первом запуске задачи для этого задания на узле. Задачу подготовки задания, настроенную в задании, можно рассматривать как шаблон задачи, при этом выполняется несколько экземпляров задач подготовки заданий, вплоть до количества узлов в пуле.

Экземпляры задачи подготовки задания должны быть проверены на наличие ошибок.
- При выполнении задачи подготовки задания перемещается задача, вызвавшая задачу подготовки задания, в [состояние](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) `preparing`; Если задача подготовки задания завершается с ошибкой, то задача, вызывающая срабатывание, вернется к состоянию `active` и не будет выполняться.  
- Все экземпляры задачи подготовки задания, которые были запущены, можно получить из задания, используя API [подготовки списка и задачи выпуска](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Как и в случае с любой задачей, доступны [сведения о выполнении](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) со свойствами, такими как `failureInfo`, `exitCode`и `result`.
- Если задачи подготовки задания завершаются неудачно, то задачи, запускающющие задание, не будут выполняться, задание не будет завершено и будет задержано. Пул может не использоваться, если нет других заданий с задачами, которые можно запланировать.

### <a name="job-release-tasks"></a>Задачи выпуска задания

Если для задания задано задание выпуска задания, то при завершении задания на каждом из узлов пула, где выполнялась задача подготовки задания, выполняется экземпляр задачи выпуска задания.  Экземпляры задачи "выпуск задания" должны быть проверены на наличие ошибок.
- Все экземпляры выполняемой задачи выпуска задания можно получить из задания с помощью функции [подготовки списка API и состояния задачи выпуска](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Как и в случае с любой задачей, доступны [сведения о выполнении](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) со свойствами, такими как `failureInfo`, `exitCode`и `result`.
- Если одна или несколько задач выпуска задания завершаются неудачно, задание будет по-прежнему завершено и переходить в состояние `completed`.

## <a name="tasks"></a>Задачи

Задачи заданий могут завершаться сбоем по нескольким причинам:

- Командная строка задачи завершается неудачно, возвращая с ненулевым кодом выхода.
- Для задачи заданы `resourceFiles`, но произошла ошибка, из-за которой не удалось загрузить один или несколько файлов.
- Для задачи заданы `outputFiles`, но произошла ошибка, которая означала, что один или несколько файлов не были отправлены.
- Превышено затраченное время для задачи, заданное свойством `maxWallClockTime` в [ограничениях](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)задачи.

Во всех случаях необходимо проверить следующие свойства на наличие ошибок и сведений об ошибках.
- Свойство Tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) содержит несколько свойств, которые предоставляют сведения об ошибке. [результат](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) указывает, завершилась ли задача по какой либо причине неудачно, с `exitCode` и `failureInfo` предоставляя дополнительные сведения о сбое.
- Задача всегда переходит в [состояние](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)`completed`, независимо от успешности ее выполнения или сбоя.

Влияние сбоев задач на задание и всех зависимостей задач должно быть рассмотрено.  Свойство [екситкондитионс](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) можно указать для задачи, чтобы настроить действие для зависимостей и для задания.
- Для зависимостей [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) определяет, блокируются или выполняются задачи, зависящие от невыполненной задачи.
- Для задания [жобактион](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) определяет, будет ли невыполненная задача приводить к отключению, завершению или оставлению неизмененного задания.

## <a name="next-steps"></a>Дальнейшие действия

Убедитесь, что приложение реализует комплексную проверку ошибок. для немедленного обнаружения и диагностики проблем может быть очень важно.
