---
title: Реагируйте на события модуля памяти Blob - Azure Event Grid IoT Edge Документы Майкрософт
description: Реагировать на события модуля памяти Blob
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086605"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Учебник: Реагировать на события хранения Blob на IoT Edge (Предварительный просмотр)
В этой статье показано, как развернуть хранилище Azure Blob storage на модуле IoT, который будет выступать в качестве издателя Event Grid для отправки событий о создании Blob и удалении Blob в Event Grid.  

Для получения обзора хранилища Azure Blob на IoT Edge [см.](../../iot-edge/how-to-store-data-blob.md)

> [!WARNING]
> Лазурный Blob Хранение на IoT Edge интеграции с Event Grid находится в предварительном просмотре

Для выполнения шагов, описанных в данном учебнике, потребуется следующее.

* **Подписка Azure** - Создайте [бесплатную учетную запись,](https://azure.microsoft.com/free) если у вас ее еще нет. 
* **Устройство Azure IoT Hub и IoT Edge** - Выполните шаги в быстром запуске для устройств [Linux](../../iot-edge/quickstart-linux.md) или [Windows,](../../iot-edge/quickstart.md) если у вас его еще нет.

## <a name="deploy-event-grid-iot-edge-module"></a>Развертывание модуля Event Grid IoT Edge

Существует несколько способов развертывания модулей на устройстве IoT Edge, и все они работают для Azure Event Grid на IoT Edge. В этой статье описаны шаги по развертыванию Event Grid на IoT Edge с портала Azure.

>[!NOTE]
> В этом уроке вы развернете модуль Event Grid без настойчивости. Это означает, что любые темы и подписки, которые вы создаете в этом учебнике, будут удалены при передислокации модуля. Для получения дополнительной информации о том, как [Persist state in Linux](persist-state-linux.md) настроить настойчивость, [Persist state in Windows](persist-state-windows.md)см. Для производственных нагрузок мы рекомендуем установить модуль Event Grid с сохранением.


### <a name="select-your-iot-edge-device"></a>Выберите устройство IoT Edge

1. Войти на [портал Azure](https://portal.azure.com)
1. Перейдите в Центр Интернета вещей.
1. Выберите **IoT Edge** из меню в разделе **Автоматическое управление устройствами.** 
1. Нажмите на идентификатор целевого устройства из списка устройств
1. Выберите **набор модулей.** Держите страницу открытой. Вы продолжите шаги в следующем разделе.

### <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Портал Azure имеет мастер, который будет просматривать вас, создавая манифест развертывания, вместо того, чтобы создавать документ JSON вручную.  В этом мастере есть три шага: **Добавление модулей**, **Указание маршрутов** и **Просмотр развертываний**.

### <a name="add-modules"></a>Добавление модулей

1. В разделе **Модули развертывания** выберите **Добавить**
1. Из типов модулей в списке выпадающих выбросов выберите **модуль IoT Edge**
1. Предоставьте имя, изображение, контейнер создать варианты контейнера:

   * **Имя**: eventgridmodule
   * **Изображение URI**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Параметры создания контейнера.**

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. Нажмите кнопку **Сохранить**.
 1. Перейти к следующему разделу, чтобы добавить модуль подписчика Azure Event Grid, прежде чем развертывать их вместе.

    >[!IMPORTANT]
    > В этом уроке вы научитесь развертывать модуль Event Grid, чтобы оба запроса HTTP/HTTPs были отключены от проверки подлинности клиентов. Для производственных нагрузок мы рекомендуем включить только запросы HTTPs и абонентов с включенной аутентификацией клиента. Для получения дополнительной информации о том, как настроить модуль Event Grid безопасно, смотрите [безопасности и аутентификации](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Развертывание Модуля IoT Edge абонента событий

В этом разделе показано, как развернуть другой модуль IoT, который будет выступать в качестве обработчика событий, к которому могут быть доставлены события.

### <a name="add-modules"></a>Добавление модулей

1. В разделе **Модули развертывания** выберите **Добавить** еще раз. 
1. Из типов модулей в списке выпадающих выбросов выберите **модуль IoT Edge**
1. Предоставьте имя, изображение и контейнер, создайте параметры контейнера:

   * **Имя**: абонент
   * **Изображение URI**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Параметры создания контейнеров:** Нет
1. Нажмите кнопку **Сохранить**.
1. Продолжить следующий раздел, чтобы добавить модуль хранения Azure Blob

## <a name="deploy-azure-blob-storage-module"></a>Развертывание модуля хранения Azure Blob

В этом разделе показано, как развернуть модуль хранения Azure Blob Storage, который будет выступать в качестве издателя Event Grid, опубликовавшего созданные и удаленные события.

### <a name="add-modules"></a>Добавление модулей

1. В разделе **Модули развертывания** выберите **Добавить**
2. Из типов модулей в списке выпадающих выбросов выберите **модуль IoT Edge**
3. Предоставьте имя, изображение и контейнер, создайте параметры контейнера:

   * **Название**: azureblobstorageoniotedge
   * **Изображение URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **Параметры создания контейнера.**

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Модуль памяти памяти Blob может публиковать события как с помощью HTTPS, так и HTTP. 
   > - Если вы включили клиентскую аутентификацию для EventGrid, убедитесь, что вы `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`обновляете значение EVENTGRID_ENDPOINT, чтобы позволить https, как это: .
   > - Также добавьте `AllowUnknownCertificateAuthority=true` другую переменную среды к вышеуказанному Json. Разговаривая с EventGrid по HTTPS, **AllowUnknownCertificateAuthority** позволяет модулю хранения доверять самоподписанным сертификатам сервера EventGrid.

4. Обновите в скопированном JSON-файле следующие сведения:

   - Замените `<your storage account name>` запоминаемым именем. Имена учетных записей должны быть от 3 до 24 символов в длину, с нижними буквами и цифрами. Нельзя использовать пробелы.

   - Замените `<your storage account key>` на 64-байтовый ключ Base64. Вы можете создать ключ с помощью таких средств, как [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Вы будете использовать эти учетные данные для доступа к хранилищу BLOB-объектов из других модулей.

   - Замените `<event grid module name>` название модуля Event Grid.
   - Заменить `<storage mount>` в соответствии с контейнерной операционной системы.
     - Для контейнеров **Linux, мой объем:/blobroot**
     - Для контейнеров для Windows**мой объем:C:/BlobRoot**

5. Нажмите кнопку **Сохранить**.
6. Нажмите **далее,** чтобы продолжить раздел маршрутов

    > [!NOTE]
    > Если вы используете Azure VM в качестве устройства края, добавьте правило входящего порта, чтобы разрешить входящий трафик в портах-хозяинах, используемых в этом учебнике: 4438, 5888, 8080 и 11002. Для получения инструкций по добавлению правила, смотрите [Как открыть порты для VM](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Маршруты настройки

Сохранить маршруты по умолчанию и выбрать **Далее,** чтобы продолжить раздел обзора

### <a name="review-deployment"></a>Проверка развертывания

1. В разделе обзора отображается манифест развертывания JSON, созданный на основе выделения в предыдущем разделе. Подтвердите, что вы видите следующие четыре модуля: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **абонент** и **azureblobstorageoniotedge,** что все развертывается.
2. Просмотрите сведения о развертывании, а затем выберите **Отправить**.

## <a name="verify-your-deployment"></a>Проверка развертывания

1. Сразу после отправки развертывания можно вернуться на страницу IoT Edge Центра Интернета вещей.
2. Выберите **устройство IoT Edge,** на которое вы ориентирулись при развертывании, чтобы открыть свои данные.
3. В деталях устройства убедитесь, что модули eventgridmodule, абонента и лазурно-бтербубского храненияданных оговорок указаны как **указанные в развертывании,** так и **зарегистрированные устройством.**

   Запуск модуля на устройстве может занять несколько секунд. Затем информация о нем передается в Центр Интернета вещей. Обновите страницу, чтобы увидеть обновленное состояние.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Публикация событий BlobCreated и BlobDeleted

1. Этот модуль автоматически создает тему **MicrosoftStorage**. Проверить, существует ли она
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Образец вывода:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Для потока HTTPS, если проверка подлинности клиента включена через ключ SAS, то ключ SAS, указанный ранее, должен быть добавлен в качестве заголовка. Таким образом, запрос локон будет:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Для потока HTTPS, если проверка подлинности клиента включена через сертификат, запрос на завиток будет:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Подписчики могут зарегистрироваться на события, опубликованные на тему. Чтобы получить любое событие, необходимо создать подписку Event Grid для темы **MicrosoftStorage.**
    1. Создайте blobsubscription.json со следующим содержанием. Для получения подробной информации о полезной нагрузке ознакомьтесь с нашей [документацией API](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > Свойство **endpointType** указывает, что абонентом является **Webhook.**  Конечная **точкаUrl** определяет URL, на котором абонент слушает события. Этот URL-адрес соответствует образцу функции Azure, который вы развернули ранее.

    2. Выполнить следующую команду, чтобы создать подписку на тему. Подтвердите, что код `200 OK`состояния HTTP .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Для потока HTTPS, если проверка подлинности клиента включена через ключ SAS, то ключ SAS, указанный ранее, должен быть добавлен в качестве заголовка. Таким образом, запрос локон будет:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Для потока HTTPS, если проверка подлинности клиента включена через сертификат, запрос на завиток будет:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Выполнить следующую команду для проверки подписки был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Образец вывода:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Для потока HTTPS, если проверка подлинности клиента включена через ключ SAS, то ключ SAS, указанный ранее, должен быть добавлен в качестве заголовка. Таким образом, запрос локон будет:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Для потока HTTPS, если проверка подлинности клиента включена через сертификат, запрос на завиток будет:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Скачать [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) и [подключить его к локальному хранилищу](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Проверка доставки событий

### <a name="verify-blobcreated-event-delivery"></a>Проверка доставки событий BlobCreated

1. Загружайте файлы как блокируйте капли в локальное хранилище из Azure Storage Explorer, и модуль будет автоматически публиковать события создания. 
2. Проверьте журналы абонентов для создания события. Выполните действия по [проверке доставки события](pub-sub-events-webhook-local.md#verify-event-delivery)

    Вывод образца:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Проверка доставки событий BlobDeleted

1. Удалить капли из локального хранилища с помощью Azure Storage Explorer, и модуль будет автоматически публиковать события удаления. 
2. Проверьте журналы абонента для удаления события. Выполните действия по [проверке доставки события](pub-sub-events-webhook-local.md#verify-event-delivery)

    Вывод образца:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Поздравляем! Вы завершили учебник. В следующих разделах приводится подробная информация о свойствах события.

### <a name="event-properties"></a>Свойства события

Вот список поддерживаемых свойств событий и их типы и описания. 

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| идентификатор | строка | Уникальный идентификатор события. |
| . | объект | Данные события хранилища BLOB-объектов. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| api | строка | Операция, вызвавшая событие. Может иметь одно из следующих значений. <ul><li>BlobCreated - разрешенные `PutBlob` значения: и`PutBlockList`</li><li>BlobDeleted - разрешенные `DeleteBlob` `DeleteAfterUpload` значения `AutoDelete`есть, и . <p>Событие `DeleteAfterUpload` генерируется, когда капля автоматически удаляется, потому что желаемое свойство deleteAfterUpload настроено на истину. </p><p>`AutoDelete`событие генерируется, когда капля автоматически удаляется, потому что срок действия желаемого значения свойства deleteAfterMinutes истек.</p></li></ul>|
| clientRequestId | строка | идентификатор запроса, предоставленного клиентом для операции API хранилища. Этот идентификатор может быть использован для соотношения с диагностическими журналами Azure Storage с помощью поля "клиент-запрос-id" в журналах, и может быть предоставлен в запросах клиентов с помощью заголовка "x-ms-client-request-id". Для получения подробной информации [см.](/rest/api/storageservices/storage-analytics-log-format) |
| requestId | строка | Идентификатор запроса, генерируемого службой для операции API хранилища. Может использоваться для корреляции журналов диагностики службы хранилища Azure с помощью поля request-id-header в журналах. Возвращается при инициации вызова API в заголовке x-ms-request-id. Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| eTag | строка | Значение, которое позволяет выполнять операции условно. |
| сontentType | строка | Тип содержимого, указанный для BLOB-объекта. |
| contentLength | Целое число | Размер большого двоичного объекта в байтах. |
| blobType | строка | Тип большого двоичного объекта. Допустимые значения: BlockBlob или PageBlob. |
| url | строка | Путь к BLOB-объекту. <br>Если клиент использует Blob REST API, то URL имеет эту структуру: * \<имя\>/\<хранилища.blob.core.windows.net/\>\>\<контейнерное имя файла.* <br>Если клиент использует Data Lake Storage REST API, то URL имеет эту структуру: * \<имя\>/\<хранилища-счета\>\>.dfs.core.windows.net/\<файл-имя-имя-* |


## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующие статьи из документации Blob Storage:

- [События хранения фильтра Blob](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Рекомендуемые методы для потребления событий памяти Blob](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

В этом уроке вы опубликовали события, создав или удалив капли в хранилище Azure Blob. Ознакомьтесь с другими учебниками, чтобы узнать, как перенаправить события в облако (Azure Event Hub или Azure IoT Hub): 

- [Переадресация событий в Сетку событий Azure](forward-events-event-grid-cloud.md)
- [Переадресация событий в Сетку событий Azure](forward-events-iothub.md)
