---
title: Реагирование на события модуля хранилища BLOB-объектов — Служба "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Реагирование на события модуля хранилища BLOB-объектов
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 10/02/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 851b5607ad5413cd1a594f788cb294ee7790e8eb
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043168"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Учебник. реагирование на события хранилища BLOB-объектов на IoT Edge (Предварительная версия)
В этой статье показано, как развернуть хранилище BLOB-объектов Azure в модуле IoT, который будет служить издателем службы "Сетка событий" для отправки событий при создании большого двоичного объекта и удалении большого двоичного объекта в службе "Сетка событий".  

Общие сведения о хранилище BLOB-объектов Azure на IoT Edge см. в статье [хранилище BLOB-объектов Azure на IOT Edge](../../iot-edge/how-to-store-data-blob.md) и его функциях.

> [!WARNING]
> Хранилище BLOB-объектов Azure в IoT Edge интеграция с сеткой событий находится на этапе предварительной версии

Для работы с этим руководством вам потребуется:

* **Подписка Azure** . Создайте [бесплатную учетную запись](https://azure.microsoft.com/free) , если она еще не создана. 
* **Центр Интернета вещей Azure и устройство IOT Edge** . выполните действия, описанные в кратком руководстве для устройств [Linux](../../iot-edge/quickstart-linux.md) или [Windows](../../iot-edge/quickstart.md) , если у вас его еще нет.

## <a name="deploy-event-grid-iot-edge-module"></a>Развертывание модуля IoT Edge сетки событий

Существует несколько способов развертывания модулей на IoT Edge устройстве, и все они работают в службе "Сетка событий Azure" на IoT Edge. В этой статье описаны шаги по развертыванию сетки событий на IoT Edge из портал Azure.

>[!NOTE]
> В этом руководстве вы развернете модуль службы "Сетка событий" без сохранения. Это означает, что все разделы и подписки, создаваемые в этом учебнике, будут удалены при повторном развертывании модуля. Дополнительные сведения о настройке сохраняемости см. в следующих статьях: [Сохранение состояния в Linux](persist-state-linux.md) или [Сохранение состояния в Windows](persist-state-windows.md). Для рабочих нагрузок рекомендуется установить модуль службы "Сетка событий" с сохраняемостью.


### <a name="select-your-iot-edge-device"></a>Выберите устройство IoT Edge

1. Войдите на [портал Azure](https://portal.azure.com)
1. Перейдите в Центр Интернета вещей.
1. Выберите **IOT Edge** в меню в разделе **Автоматическое управление устройствами** . 
1. Щелкните идентификатор целевого устройства в списке устройств.
1. Щелкните **Set Modules** (Настроить модули). Не закрывайте страницу. Шаги, описанные в следующем разделе, будут продолжены.

### <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. В портал Azure имеется мастер, который поможет вам создать манифест развертывания вместо создания документа JSON вручную.  В этом мастере есть три шага: **Добавление модулей**, **Указание маршрутов** и **Просмотр развертываний**.

### <a name="add-modules"></a>Добавление модулей

1. В разделе **модули развертывания** выберите **Добавить** .
1. В раскрывающемся списке типы модулей выберите **IOT Edge Module (модуль** ).
1. Укажите параметры "имя", "изображение", "создать контейнер" для контейнера:

   * **Имя**: евентгридмодуле
   * **URI изображения**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Параметры создания контейнера.**

    ```json
        {
          "Env": [
           "inbound:serverAuth:tlsPolicy=enabled",
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
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
 1. Нажмите кнопку **Сохранить**
 1. Перейдите к следующему разделу, чтобы добавить модуль "функции Azure"

    >[!IMPORTANT]
    > В этом руководстве вы развернете модуль сетки событий, чтобы разрешить запросы HTTP/HTTPs, проверка подлинности клиента отключена и разрешение подписчиков HTTP. Для рабочих нагрузок рекомендуется включить проверку подлинности клиента только по протоколам HTTPs и подписчикам. Дополнительные сведения о безопасной настройке модуля сетки событий см. в разделе [безопасность и проверка подлинности](security-authentication.md).
    

## <a name="deploy-azure-function-iot-edge-module"></a>Развертывание модуля IoT Edge функции Azure

В этом разделе показано, как развернуть модуль IoT службы "функции Azure", который будет действовать как подписчик службы "Сетка событий", на который могут доставляться события.

>[!IMPORTANT]
>В этом разделе вы развернете пример модуля подписки на основе функций Azure. Это может быть любой настраиваемый модуль IoT, который может прослушивать запросы HTTP POST.

### <a name="add-modules"></a>Добавление модулей

1. В разделе **модули развертывания** выберите **Добавить** еще раз. 
1. В раскрывающемся списке типы модулей выберите **IOT Edge Module (модуль** ).
1. Укажите параметры имя, образ и контейнер для создания контейнера:

   * **Имя**: подписчик
   * **URI изображения**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber-azfunc:latest`
   * **Параметры создания контейнера.**

       ```json
            {
              "HostConfig": {
                "PortBindings": {
                  "80/tcp": [
                    {
                      "HostPort": "8080"
                    }
                  ]
                }
              }
            }
       ```

1. Нажмите кнопку **Сохранить**
1. Перейдите к следующему разделу, чтобы добавить модуль хранилища BLOB-объектов Azure.

> [!NOTE]
> Модуль хранилища BLOB-объектов публикует события с помощью HTTP. Убедитесь, что модуль "Сетка событий" разрешает запросы HTTP и HTTPS со следующей конфигурацией: `inbound:serverAuth:tlsPolicy=enabled`.

## <a name="deploy-azure-blob-storage-module"></a>Развертывание модуля хранилища BLOB-объектов Azure

В этом разделе показано, как развернуть модуль хранилища BLOB-объектов Azure, который будет действовать как издатель службы "Сетка событий", который создает и удаляет события.

### <a name="add-modules"></a>Добавление модулей

1. В разделе **модули развертывания** выберите **Добавить** .
2. В раскрывающемся списке типы модулей выберите **IOT Edge Module (модуль** ).
3. Укажите параметры имя, образ и контейнер для создания контейнера:

   * **Имя**: азуреблобсторажеониотедже
   * **URI изображения**: MCR.Microsoft.com/Azure-Blob-Storage:1.2.2-Preview
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

4. Обновите в скопированном JSON-файле следующие сведения:

   - Замените `<your storage account name>` запоминаемым именем. Длина имени учетной записи должна составлять от 3 до 24 символов, а строчные буквы и цифры. Без пробелов.

   - Замените `<your storage account key>` на 64-байтовый ключ Base64. Вы можете создать ключ с помощью таких средств, как [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Вы будете использовать эти учетные данные для доступа к хранилищу BLOB-объектов из других модулей.

   - Замените `<event grid module name>` именем модуля сетки событий.
   - Замените `<storage mount>` в соответствии с операционной системой контейнера.
     - Для контейнеров Linux **My-Volume:/блобрут**
     - Для контейнеров Windows**My-Volume: C:/блобрут**

5. Нажмите кнопку **Сохранить**
6. Нажмите кнопку " **Далее** ", чтобы перейти к разделу "маршруты"

 ### <a name="setup-routes"></a>Настройка маршрутов

Сохранить маршруты по умолчанию и нажать кнопку **Далее** , чтобы перейти к разделу "Проверка"

### <a name="review-deployment"></a>Проверка развертывания

1. В разделе "Проверка" показан манифест развертывания JSON, созданный на основе параметров, сделанных в предыдущем разделе. Убедитесь, что вы видите следующие четыре модуля: **$edgeAgent**, **$edgeHub**, **евентгридмодуле**, **Subscriber** и **азуреблобсторажеониотедже** , которые развертываются.
2. Просмотрите сведения о развертывании, а затем выберите **Отправить**.

## <a name="verify-your-deployment"></a>Проверка развертывания

1. После отправки развертывания вы вернетесь на страницу IoT Edge центра Интернета вещей.
2. Выберите **устройство IOT Edge** , для которого предназначается развертывание, чтобы открыть сведения о нем.
3. В сведениях об устройстве убедитесь, что модули евентгридмодуле, подписчик и азуреблобсторажеониотедже указаны как **указанные в развертывании** и **переданы по устройствам**.

   Запуск модуля на устройстве может занять несколько секунд. Затем информация о нем передается в Центр Интернета вещей. Обновите страницу, чтобы увидеть обновленное состояние.

## <a name="publish-created-and-deleted-events"></a>Публикация созданных и удаленных событий

1. Этот модуль автоматически создает раздел **микрософтстораже**. Убедитесь, что он существует
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Пример выходных данных:

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

2. Подписчики могут регистрироваться для событий, опубликованных в разделе. Чтобы получить любое событие, необходимо создать подписку на сетку событий для **микрософтстораже** .
    1. Создайте блобсубскриптион. JSON со следующим содержимым. Дополнительные сведения о полезных данных см. в [документации по API](api.md) .

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > Свойство **endpointType** указывает, что подписчик является веб- **перехватчиком**.  **EndpointUrl** указывает URL-адрес, по которому подписчик прослушивает события. Этот URL-адрес соответствует образцу функции Azure, который вы развернули ранее.

    2. Выполните следующую команду, чтобы создать подписку для раздела. Убедитесь, что отображается код состояния HTTP `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    3. Выполните следующую команду, чтобы проверить, успешно ли создана подписка. Должен возвращаться код состояния HTTP, 200 ОК.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    Пример выходных данных:

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
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

2. Скачайте [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) и [подключите его к локальному хранилищу](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Проверка доставки событий

### <a name="verify-blobcreated-event-delivery"></a>Проверка доставки событий BlobCreated

1. Отправьте файлы как блочные BLOB-объекты в локальное хранилище из Обозреватель службы хранилища Azure, и модуль будет автоматически публиковать события создания. 
2. Просмотрите журналы подписчика для создания события. Выполните действия для [проверки доставки событий](pub-sub-events-webhook-local.md#verify-event-delivery) .

    Пример выходных данных:

    ```json
            Received event data [
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
          ]
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Проверка доставки событий BlobDeleted

1. Удалите BLOB-объекты из локального хранилища с помощью Обозреватель службы хранилища Azure, и модуль будет автоматически публиковать события удаления. 
2. Просмотрите журналы подписчика для события удаления. Выполните действия для [проверки доставки событий](pub-sub-events-webhook-local.md#verify-event-delivery) .

    Пример выходных данных:
    
    ```json
            Received event data [
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
          ]
    ```

Поздравляем! Вы завершили работу с руководством. В следующих разделах содержатся сведения о свойствах событий.

### <a name="event-properties"></a>Свойства события

Ниже приведен список поддерживаемых свойств событий, их типов и описаний. 

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| Раздел | string | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | string | Определенный издателем путь к субъекту событий. |
| eventType | string | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | string | Время создания события с учетом времени поставщика в формате UTC. |
| id | string | Уникальный идентификатор события. |
| data | object | Данные события хранилища BLOB-объектов. |
| dataVersion | string | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | string | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | Тип | Описание |
| -------- | ---- | ----------- |
| api | string | Операция, вызвавшая событие. Может принимать одно из следующих значений: <ul><li>BlobCreated — допустимые значения: `PutBlob` и `PutBlockList`</li><li>BlobDeleted — допустимые значения: `DeleteBlob`, `DeleteAfterUpload` и `AutoDelete`. <p>Событие `DeleteAfterUpload` создается при автоматическом удалении большого двоичного объекта, так как требуемое свойство Делетеафтеруплоад имеет значение true. </p><p>`AutoDelete` событие создается при автоматическом удалении большого двоичного объекта из-за истечения срока действия требуемого значения свойства Делетеафтерминутес.</p></li></ul>|
| clientRequestId | string | предоставленный клиентом идентификатор запроса для операции API хранилища. Этот идентификатор можно использовать для сопоставления с журналами диагностики службы хранилища Azure с помощью поля "Client-Request-ID" в журналах и может быть предоставлено в запросах клиента с помощью заголовка "x-MS-Client-Request-ID". Дополнительные сведения см. в разделе [Формат журнала](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Создаваемый службой идентификатор запроса для операции API хранилища. Может использоваться для корреляции журналов диагностики службы хранилища Azure с помощью поля request-id-header в журналах. Возвращается при инициации вызова API в заголовке x-ms-request-id. Ознакомьтесь со статьей [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Формат журналов Аналитики Службы хранилища). |
| eTag | string | Значение, которое позволяет выполнять операции условно. |
| сontentType | string | Тип содержимого, указанный для BLOB-объекта. |
| contentLength | целое число | Размер большого двоичного объекта в байтах. |
| blobType | string | Тип большого двоичного объекта. Допустимые значения: BlockBlob или PageBlob. |
| URL-адрес | string | Путь к BLOB-объекту. <br>Если клиент использует REST API больших двоичных объектов, URL-адрес имеет следующую структуру: *\<storage-Account-name \>. blob.core.windows.net/\<container-name \> / \<file-name \>* . <br>Если клиент использует Data Lake Storage REST API, URL-адрес имеет следующую структуру: *\<storage-Account-name \>. dfs.core.windows.net/\<file-System-name \> / \<file-name \>* . |


## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь со следующими статьями в документации по хранилищу BLOB-объектов:

- [Фильтрация событий хранилища BLOB-объектов](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Рекомендуемые методики использования событий хранилища BLOB-объектов](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

В этом руководстве вы опубликовали события, создавая или удаляя большие двоичные объекты в хранилище BLOB-объектов Azure. Дополнительные сведения о пересылке событий в облако (концентратор событий Azure или центр Интернета вещей Azure) см. в других руководствах. 

- [Пересылка событий в службу "Сетка событий Azure"](forward-events-event-grid-cloud.md)
- [Пересылка событий в центр Интернета вещей Azure](forward-events-iothub.md)