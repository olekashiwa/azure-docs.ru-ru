---
title: Отправка и получение событий через Центры событий Azure с помощью Python (последняя версия)
description: В статье описано, как создать приложение Python, которое отправляет события или получает их из службы "Центры событий Azure" с помощью последнего пакета azure-eventhub версии 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906362"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Отправка событий или получение событий из концентраторов событий с помощью Python (azure-eventhub версии 5)

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в режиме реального времени, а также с помощью адаптеров пакетной обработки или хранения. Дополнительные сведения см. в статье [Центры событий Azure — платформа потоковой передачи больших данных и служба приема событий](event-hubs-about.md) и [Features and terminology in Azure Event Hubs](event-hubs-features.md) (Функции и терминология в Центрах событий Azure).

В этом кратком руководстве описано, как создать приложения Python, которые отправляют или получают события через концентратор событий.

> [!IMPORTANT]
> В рамках этого краткого руководства используется версия 5 пакета SDK Центров событий Azure для Python. Краткое руководство по использованию пакета SDK для Python версии 1 см. [здесь](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством необходимо следующее:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- Активное пространство имен Центров событий и концентратор событий. Для их создания следуйте указаниям, приведенным здесь — [Краткое руководство. Создание концентратора событий с помощью портала Azure](event-hubs-create.md). Запишите имена пространства имен и концентратора событий, которые пригодятся далее в этом кратком руководстве.
- Имя ключа общего доступа и значение первичного ключа для пространства имен Центров событий. Получите имя и значение ключа доступа, следуя инструкциям в разделе [Get connection string from the portal](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) (Получение строки подключения на портале). По умолчанию для ключа доступа используется имя *RootManageSharedAccessKey*. Запишите имя ключа и значение первичного ключа, которые потребуются далее в этом кратком руководстве.
- Python 2.7, 3.5 или более поздней версии, для которого установлено и обновлено PIP.
- Пакет Python для Центров событий. 

    Чтобы установить этот пакет, выполните следующую команду в окне командной строки, где путь к Python сохранен в переменной окружения path.

    ```cmd
    pip install azure-eventhub
    ```

    Установите следующий пакет для получения событий с помощью хранилища BLOB-объектов Azure в качестве хранилища контрольных точек.

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Отправка событий
В этом разделе вы создадите скрипт Python для отправки событий в концентратор событий, созданный ранее.

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте сценарий с именем *send.py*. Этот сценарий отправляет пакет событий в концентратор событий, созданный ранее.
3. Скопируйте приведенный ниже код в файл *send.py*.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Полный исходный код, включая информационные комментарии, можно найти на странице [GitHub send_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Получение событий
В этом кратком руководстве в качестве хранилища контрольных точек используется хранилище BLOB-объектов Azure. Хранилище контрольных точек используется для сохранения контрольных точек (т. е. последних позиций чтения).  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Создание учетной записи хранения Azure и контейнера больших двоичных объектов
Создайте учетную запись хранения Azure и контейнер больших двоичных объектов в ней, выполнив следующие действия.

1. [Создайте учетную запись хранения Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Создание контейнера больших двоичных объектов](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Получение строки подключения к учетной записи хранения](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string).

Обязательно запишите строку подключения и имя контейнера для последующего использования в коде получения.


### <a name="create-a-python-script-to-receive-events"></a>Создание сценария Python для получения событий

В этом разделе вы создадите скрипт Python для получения событий из концентратора событий.

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте сценарий с именем *recv.py*.
3. Вставьте в *recv.py* следующий код.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Полный исходный код, включая дополнительные информационные комментарии, можно найти на странице [GitHub recv_with_checkpoint_store_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Запуск приложения получателя

Чтобы выполнить сценарий, откройте окно командной строки с Python в пути, а затем выполните следующую команду.

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Запуск приложения отправителя

Чтобы выполнить сценарий, откройте окно командной строки с Python в пути, а затем выполните следующую команду.

```bash
python send.py
```

В окне получателя должны отобразиться сообщения, отправленные в концентратор событий.


## <a name="next-steps"></a>Дальнейшие действия
В этом кратком руководстве события отправлены и получены асинхронно. Чтобы узнать, как отправлять и получать события синхронно, перейдите на страницу [GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Для всех примеров (синхронных и асинхронных) в GitHub перейдите на страницу [Azure Event Hubs client library for Python Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples) (Клиентская библиотека Центров событий Azure для примеров Python).
