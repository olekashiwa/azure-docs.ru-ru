---
title: Повторное включение защиты виртуальных машин Azure в основном регионе с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описывается, как повторно включить защиту виртуальных машин Azure после отработки отказа, дополнительного в основном регионе, с помощью Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 818c053c22cfa47cac0f4f6a19349cf239d3cdec
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368589"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Повторное включение защиты виртуальных машин Azure в основном регионе после отработки отказа

При [отработки отказа](site-recovery-failover.md) виртуальных машин Azure из одного региона в другой с помощью [Azure Site Recovery](site-recovery-overview.md)виртуальные машины загружаются в дополнительном регионе в **незащищенном** состоянии. Если вы хотите восстановить работоспособность виртуальных машин в основном регионе, выполните следующие действия.

1. Повторно включите защиту виртуальных машин в дополнительном регионе, чтобы запустить репликацию в основном регионе.
1. После завершения повторного включения защиты и репликации виртуальных машин можно выполнить отработку отказа с вторичного на основной регион.

## <a name="prerequisites"></a>предварительные требования

- Данные о выполнении отработки отказа виртуальной машины из основного в дополнительный регион необходимо фиксировать.
- Основной целевой регион должен быть доступен и у вас должен быть доступ к ресурсам или возможность создавать их в этом регионе.

## <a name="reprotect-a-vm"></a>Повторное включение защиты виртуальной машины

1. В разделе **Хранилище** > **Реплицированные элементы** щелкните правой кнопкой мыши виртуальную машину, для которой проводилась отработка отказа, и выберите **Защитить повторно**. Должно отображаться направление повторной защиты от дополнительного в основной.

   ![Повторное включение защиты](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Просмотрите сведения о группе ресурсов, сети, хранилище и группах доступности. Нажмите кнопку **ОК**. Если какие – либо ресурсы помечены как новые, они будут созданы как часть процесса повторного включения защиты.
1. Задание повторной защиты заполняет целевой сайт актуальными данными. После завершения задания происходит разностная репликация. Затем вы можете выполнить отработку отказа на основном сайте. Используя параметр настройки, вы можете выбрать учетную запись хранения или сеть для использования во время повторного включения защиты.

   ![Параметр "Настройка"](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Настройка параметров повторного включения защиты

Во время повторного включения защиты можно настроить следующие свойства целевой виртуальной машины.

![Настройка](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Свойство |Примечания  |
|---------|---------|
|Целевая группа ресурсов | Измените целевую группу ресурсов, в которой создана виртуальная машина. В целях повторного включения защиты целевая виртуальная машина удаляется. Вы можете выбрать новую группу ресурсов, в которой нужно создать виртуальную машину после отработки отказа. |
|Целевая виртуальная сеть | Во время включения задания повторной защиты невозможно изменить целевую сеть. Чтобы изменить сети, выполните повторное сопоставление сети. |
|Целевое хранилище (вторичная виртуальная машина не использует управляемые диски) | Вы можете изменить учетную запись хранения, которую виртуальная машина использует после отработки отказа. |
|Реплика управляемых дисков (дополнительная виртуальная машина, которая использует управляемые диски) | Site Recovery создает управляемые диски реплики в основном регионе, чтобы дублировать управляемые диски дополнительной виртуальной машины. |
|Хранилище кэша | Вы можете указать учетную запись хранения кэша, которую нужно использовать во время репликации. По умолчанию создается новая учетная запись хранения кэша, если она не существует. |
|Группа доступности | Если виртуальная машина в дополнительном регионе является частью группы доступности, вы можете выбрать группу доступности для целевой виртуальной машины в основном регионе. По умолчанию Site Recovery пытается найти и использовать имеющуюся группу доступности в основном регионе. Во время настройки можно указать новую группу доступности. |

### <a name="what-happens-during-reprotection"></a>Что происходит при повторном включении защиты?

По умолчанию происходит следующее:

1. Учетная запись хранения кэша создается в регионе, в котором на виртуальной машине выполняется отработка отказа.
1. Если целевая учетная запись хранения (исходная учетная запись хранения в основном регионе) не существует, будет создана новая. Назначенное имя учетной записи хранения — это имя учетной записи хранения, используемой вторичной виртуальной машиной с суффиксом `asr`.
1. Если виртуальная машина использует управляемые диски, управляемые диски реплики создаются в основном регионе для хранения данных, реплицированных с дисков дополнительной виртуальной машины.
1. Если целевая группа доступности не существует, она создается в рамках задания повторного включения защиты (при необходимости). Если вы настроили параметры повторного включения защиты, то используется выбранный набор.

Если запустить задание повторной защиты при наличии целевой виртуальной машины, происходит следующее.

1. Целевое расположение виртуальной машины выключается, если она запущена.
1. Если виртуальная машина использует управляемые диски, создается копия исходного диска с суффиксом `-ASRReplica`. Исходные диски удаляются. Для репликации используются `-ASRReplica` копии.
1. Если виртуальная машина использует неуправляемые диски, диски данных целевой виртуальной машины отключаются и используются для репликации. Создается копия диска операционной системы, которая подключается к виртуальной машине. Диск исходной операционной системы отключается и используется для репликации.
1. Синхронизируются только изменения между исходным и целевым дисками. Разница в файлах определяется путем сравнения обоих дисков с последующей передачей. Ниже приведена проверка предполагаемого времени для завершения повторного включения защиты.
1. После завершения синхронизации начинается разностная репликация и создается точка восстановления в соответствии с политикой репликации.

При активации задания повторной защиты, если Целевая виртуальная машина и диски не существуют, происходит следующее:

1. Если виртуальная машина использует управляемые диски, диски реплики создаются с суффиксом `-ASRReplica`. Для репликации используются `-ASRReplica` копии.
1. Если виртуальная машина использует неуправляемые диски, реплики дисков создаются в целевой учетной записи хранения.
1. Все содержимое дисков копируется из региона отработки отказа в новый целевой регион.
1. После завершения синхронизации начинается разностная репликация и создается точка восстановления в соответствии с политикой репликации.

#### <a name="estimated-time-to-do-the-reprotection"></a>Предполагаемое время повторного включения защиты

В большинстве случаев Azure Site Recovery не реплицирует полные данные в исходный регион.
Объем реплицируемых данных определяется следующими условиями.

1. Если исходные данные виртуальной машины удалены, повреждены или недоступны по какой-либо причине, например при изменении или удалении группы ресурсов, то во время повторного включения защиты произойдет полная начальная репликация, так как в исходном регионе отсутствуют данные, доступные для использования.
1. Если исходные данные виртуальной машины доступны, то вычисляются только разностные копии путем сравнения дисков и передаваемых данных. Просмотрите таблицу ниже, чтобы получить предполагаемое время.

|Пример ситуации | Время, затраченное на повторное включение защиты |
|---|---|
|Исходный регион содержит 1 виртуальную машину с дисковым стандартом 1 ТБ.<br/>Используется только 127 Гб данных, а остальная часть диска пуста.<br/>Тип диска — Standard с пропускной способностью 60 MiB/с.<br/>После отработки отказа изменения данных не изменяются.| Приблизительное время: 45 минут – 1,5 часов.<br/>При повторном включении защиты Site Recovery заполнит контрольную сумму всех данных, которая займет 127 Гб/45 МБ, приблизительно 45 минут.<br/>Для автоматического масштабирования Site Recovery требуется некоторое время, приблизительно 20-30 минут.<br/>Не взимается плата за исходящий трафик. |
|Исходный регион содержит 1 виртуальную машину с дисковым стандартом 1 ТБ.<br/>Используется только 127 Гб данных, а остальная часть диска пуста.<br/>Тип диска — Standard с пропускной способностью 60 MiB/с.<br/>45 ГБ данных после отработки отказа.| Приблизительное время: 1 час – 2 часа.<br/>При повторном включении защиты Site Recovery заполнит контрольную сумму всех данных, которая займет 127 Гб/45 МБ, приблизительно 45 минут.<br/>Время на перенос для применения изменений 45 Гб с 45 ГБ/45 Мбит/с, примерно 17 минут.<br/>Плата за исходящий трафик будет составлять 45 ГБ данных, а не по контрольной сумме. |

## <a name="next-steps"></a>Дальнейшие действия

После включения защиты виртуальной машины вы можете инициировать отработку отказа. Отработка отказа завершает работу виртуальной машины в дополнительном регионе и создает и загружает виртуальную машину в основном регионе с коротким временем простоя во время этого процесса. Мы рекомендуем выбрать соответствующее время для этого процесса и выполнить тестовую отработку отказа перед запуском полной отработки отказа на первичном сайте. Дополнительные [сведения](site-recovery-failover.md) об отработке отказа Azure Site Recovery.
