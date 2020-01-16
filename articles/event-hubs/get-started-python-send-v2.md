---
title: Отправка и получение событий с помощью Python — концентраторы событий Azure | Документация Майкрософт
description: В этой статье представлено пошаговое руководство по созданию приложения Python, которое отправляет события в концентраторы событий Azure.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: 11548ba180a2dd6541240431d670812448c502b8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981584"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Отправка событий или получение событий из концентраторов событий с помощью Python

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Служба "Центры событий" может обрабатывать и сохранять события и данные, в том числе телеметрические, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в режиме реального времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом учебнике описывается создание приложений Python для отправки событий или получения событий из концентратора событий.

> [!IMPORTANT]
> В этом кратком руководстве используется версия 5 пакета SDK для концентраторов событий Azure для Python. Краткий старт, использующий старую версию 1 пакета SDK для Python, см. в [этой статье](event-hubs-python-get-started-send.md). Если вы используете пакет SDK версии 1, рекомендуется перенести код в последнюю версию. Дополнительные сведения см. в разделе [руководств по миграции](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- Активное пространство имен концентраторов событий и концентратор событий, созданное с помощью инструкций в разделе [Краткое руководство по созданию концентратора событий с использованием портал Azure](event-hubs-create.md). Запишите имена пространства имен и концентратора событий, которые пригодятся далее в этом пошаговом руководстве.
- Имя ключа общего доступа и значение первичного ключа для пространства имен Центров событий. Получите имя и значение ключа доступа, следуя инструкциям в разделе [Get connection string from the portal](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) (Получение строки подключения на портале). По умолчанию для ключа доступа используется имя **RootManageSharedAccessKey**. Скопируйте имя ключа и значение первичного ключа, которые потребуются далее в этом пошаговом руководстве.
- Python 2,7 и 3,5 или более поздней версии с `pip` установлен и обновлен.
- Пакет Python для Центров событий. Чтобы установить этот пакет, выполните следующую команду в окне командной строки, где путь к Python сохранен в переменной окружения path.

    ```cmd
    pip install azure-eventhub
    ```

    Установите этот пакет для получения событий с помощью хранилища BLOB-объектов Azure в качестве хранилища контрольных точек.

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>Отправка событий
В этом разделе вы создадите скрипт Python для отправки событий в концентратор событий, созданный ранее.

1. Откройте редактор Python, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте сценарий с именем **send.py**. Этот сценарий отправляет пакет событий в концентратор событий, созданный ранее.
3. Вставьте следующий код в send.py. Дополнительные сведения см. в комментариях к коду.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()

            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Полный исходный код с очень полезными комментариями см. в [этом файле на сайте GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py) .

## <a name="receive-events"></a>Получение событий
В этом кратком руководстве в качестве хранилища контрольных точек используется хранилище BLOB-объектов Azure. Хранилище контрольных точек используется для сохранения контрольных точек (Последнее расположение чтения).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Создание хранилища Azure и контейнера больших двоичных объектов
Выполните следующие действия, чтобы создать учетную запись хранения Azure в контейнере BLOB-объектов.

1. [Создайте учетную запись хранения Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Создание контейнера больших двоичных объектов](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Получение строки подключения к учетной записи хранения](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Запишите строку подключения и имя контейнера. Они будут использоваться в коде получения.


### <a name="create-python-script-to-receive-events"></a>Создание скрипта Python для получения событий

В этом разделе вы создадите скрипт Python для получения событий из концентратора событий:

1. Откройте редактор Python, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте сценарий с именем **recv.py**.
3. Вставьте следующий код в recv.py. Дополнительные сведения см. в комментариях к коду.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # print the event data
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)

    async def main():
        # create an Azure blob checkpoint store to store the checkpoints
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Полный исходный код с очень полезными комментариями см. в [этом файле на сайте GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py) .


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

В окне приемника должны отобразиться сообщения, отправленные в концентратор событий.


## <a name="next-steps"></a>Дальнейшие действия
В этом кратком руководстве события отправляются и получаются асинхронно. Сведения о синхронной отправке и получении событий см. в разделе образцы в [этом расположении](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Все примеры (Sync и Async) можно найти [здесь](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
