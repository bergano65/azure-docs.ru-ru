---
title: Развертывание модуля на хранилище BLOB-объектов устройств — Edge Интернета вещей Azure | Документация Майкрософт
description: Разверните модуль хранилища BLOB-объектов Azure на вашем устройстве IoT Edge для хранения данных на границе.
author: kgremban
ms.author: kgremban
ms.date: 05/21/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: philmea
ms.openlocfilehash: d844e81de9cfb556e91ab5c0d5a8074c822cce0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65990472"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Развертывание хранилища BLOB-объектов Azure в модуле IoT Edge на устройстве

Существует несколько способов развертывание модулей на устройстве IoT Edge и все они работают для хранилища BLOB-объектов на модули Edge Интернета вещей. Два простейших метода — это портал Azure или шаблоны Visual Studio Code.

## <a name="prerequisites"></a>Технические условия

- [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.
- [Устройство IoT Edge](how-to-register-device-portal.md) с установленной средой выполнения IoT Edge.
- [Visual Studio Code](https://code.visualstudio.com/) и [средств Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) при развертывании из Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Развертывание с помощью портала Azure

Портала Azure поможет выполнить шаги для создания манифеста развертывания и отправки развертывания на устройстве IoT Edge.

### <a name="select-your-device"></a>Выбор устройства

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей.
1. В меню выберите **IoT Edge**.
1. Щелкните идентификатор целевого устройства в списке устройств.
1. Щелкните **Set Modules** (Настроить модули).

### <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Портал Azure содержит мастер, который поможет создать манифест развертывания, вместо создания документа JSON вручную. Манифест создается в три этапа: **Добавление модулей**, **Указание маршрутов** и **Просмотр развертываний**.

#### <a name="add-modules"></a>Добавление модулей

1. В разделе **Модули развертывания** на этой странице выберите **Добавить**.

1. В списке типов модулей в раскрывающемся списке выберите **модуля IoT Edge**.

1. Введите имя для модуля, а затем укажите образ контейнера:

   - **Имя** -azureblobstorageoniotedge
   - **URI образа** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge — с учетом регистра, когда вызов к модулям, а также пакет SDK службы хранилища по умолчанию в нижний регистр. Несмотря на то что имя модуля в [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) — **AzureBlobStorageonIoTEdge**, изменение имени в нижний регистр помогает проверить, подключениями к хранилищу BLOB-объектов Azure на модуль Edge Интернета вещей не прерывается.

1. Значение по умолчанию **параметры создания контейнера** значения определяют привязки портов, необходимые для вашего контейнера, но также необходимо добавить сведения об учетной записи хранения и привязки для каталога хранилища на вашем устройстве. Замените JSON по умолчанию на портале код JSON:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Обновите в скопированном JSON-файле следующие сведения:

   - Замените `<your storage account name>` запоминаемым именем. Имена учетных записей должна содержать 3 до 24 знаков, с строчные буквы и цифры. Без пробелов.

   - Замените `<your storage account key>` на 64-байтовый ключ Base64. Вы можете создать ключ с помощью таких средств, как [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Вы будете использовать эти учетные данные для доступа к хранилищу BLOB-объектов из других модулей.

   - Замените `<storage directory bind>` в соответствии с вашей операционной системы контейнера. Укажите имя [тома](https://docs.docker.com/storage/volumes/) или абсолютный путь к каталогу на устройстве IoT Edge, где модуль BLOB-объектов должен хранить данные. Привязка каталога хранения сопоставляет указанное расположение на устройстве с определением расположения в модуле.

     - Для контейнеров Linux, имеет формат  *\<путь к хранилищу >: / blobroot*. Например **/srv/containerdata: / blobroot** или **Мои тома: / blobroot**.
     - Для контейнеров Windows, имеет формат  *\<путь к хранилищу >: C: / BlobRoot*. Например **C: / ContainerData:C: / BlobRoot** или **my-том: C: / blobroot**.

     > [!IMPORTANT]
     > Не изменяйте вторую половину значения привязки каталога хранения, которая указывает на определенное расположение в модуле. Привязка каталога хранения всегда должна заканчиваться на **:/blobroot** — для контейнеров Linux и **:C:/BlobRoot** — для контейнеров Windows.

    ![Изменение значений для параметров создания контейнера модуля на портале](./media/how-to-store-data-blob/edit-module.png)

1. Задайте [распределение по уровням](how-to-store-data-blob.md#tiering-properties) и [time-to-live](how-to-store-data-blob.md#time-to-live-properties) свойства для модуля, скопируйте приведенный ниже код JSON и вставьте его в **требуемые свойства двойника модуля набор** поле. Настройте соответствующее значение каждого свойства, сохраните его и продолжить развертывание.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

      ```

   ![Задайте свойства распределения по уровням и time-to-live](./media/how-to-store-data-blob/iotedge_custom_module.png)

   Сведения о настройке распределения по уровням и срок ЖИЗНИ, после развертывания модуля, см. в разделе [изменить Двойник модуля](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Дополнительные сведения о требуемых свойств, см. в разделе [определение или обновление требуемых свойств](module-composition.md#define-or-update-desired-properties).

1. Щелкните **Сохранить**.

1. Щелкните **Далее**, чтобы перейти на страницу маршрутов.

#### <a name="specify-routes"></a>Настройка маршрутов

Сохранить маршруты по умолчанию и выберите **Далее** для перейдите к разделу проверки.

#### <a name="review-deployment"></a>Проверка развертывания

В разделе обзорной информации вы увидите манифест развертывания в формате JSON, который был создан на основе параметров, выбранных вами в двух предыдущих разделах. Существуют также два модуля объявлено, что вы не добавляли: **$edgeAgent** и **$edgeHub**. Эти два модуля составляют [среду выполнения IoT Edge](iot-edge-runtime.md) и являются обязательными для каждого развертывания.

Просмотрите сведения о развертывании, а затем выберите **Отправить**.

### <a name="verify-your-deployment"></a>Проверка развертывания

Сразу после отправки развертывания можно вернуться на страницу **IoT Edge** Центра Интернета вещей.

1. Выберите устройство IoT Edge, на которое отправили развертывание, чтобы открыть сведения о нем.
1. В сведениях об устройстве убедитесь, что для модуля службы хранилища BLOB-объектов настроены параметры **Указано в развертывании** и **Данные, полученные от устройства**.

Запуск модуля на устройстве может занять несколько секунд. Затем информация о нем передается в Центр Интернета вещей. Обновите страницу, чтобы увидеть обновленное состояние.

## <a name="deploy-from-visual-studio-code"></a>Развертывание из Visual Studio Code

Azure IoT Edge предоставляет шаблоны в Visual Studio Code для разработки решений для границы. Создание нового решения IoT Edge с модулем хранилища BLOB-объектов и настроить манифест развертывания, выполните следующие действия.

1. Выберите **Представление** > **Палитра команд**.

1. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge).

   ![Запуск команды создания решения IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Следуйте инструкциям на экране в палитре команд для создания решения.

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработки для Visual Studio Code для создания файлов решения. |
   | Введите название решения. | Введите описательное имя решения или примите имя по умолчанию **EdgeSolution**. |
   | Выбор шаблона модуля | Выберите **Existing Module (Enter full image URL)** (Имеющийся модуль (введите полный URL-адрес образа)). |
   | Указание имени модуля | Введите имя модуля, используя знаки нижнего регистра, например **azureblobstorage**.<br /><br />В модуле IoT Edge очень важно использовать знаки нижнего регистра для имени хранилища BLOB-объектов Azure. IoT Edge учитывает регистр при ссылке на модули, а название пакета SDK службы хранилища по умолчанию преобразовывается в нижний регистр. |
   | Указание образа Docker для модуля | Укажите универсальный код ресурса (URI) образа: **mcr.microsoft.com/azure-blob-storage:latest**. |

   Visual Studio Code принимает предоставленные сведения, создает решение IoT Edge, а затем загружает его в новом окне. Шаблон решения создает шаблон манифеста развертывания, который содержит образ модуля хранилища BLOB-объектов, но вам нужно настроить параметры создания модуля.

1. Откройте *deployment.template.json* в рабочей области нового решения и найдите раздел **modules**. Внесите следующие изменения конфигурации.

   1. Удалите модуль **tempSensor**, так как он не требуется для этого развертывания.

   1. Скопируйте и вставьте следующий код в `createOptions` поля:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Обновление модуля createOptions - Visual Studio Code](./media/how-to-store-data-blob/create-options.png)

1. Замените `<your storage account name>` запоминаемым именем. Имена учетных записей должна содержать 3 до 24 знаков, с строчные буквы и цифры. Без пробелов.

1. Замените `<your storage account key>` на 64-байтовый ключ Base64. Вы можете создать ключ с помощью таких средств, как [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Вы будете использовать эти учетные данные для доступа к хранилищу BLOB-объектов из других модулей.

1. Замените `<storage directory bind>` в соответствии с вашей операционной системы контейнера. Укажите имя [тома](https://docs.docker.com/storage/volumes/) или абсолютный путь к каталогу на устройстве IoT Edge, где модуль BLOB-объектов должен хранить данные. Привязка каталога хранения сопоставляет указанное расположение на устройстве с определением расположения в модуле.  

      - Для контейнеров Linux, имеет формат  *\<путь к хранилищу >: / blobroot*. Например **/srv/containerdata: / blobroot** или **Мои тома: / blobroot**.
      - Для контейнеров Windows, имеет формат  *\<путь к хранилищу >: C: / BlobRoot*. Например **C: / ContainerData:C: / BlobRoot** или **my-том: C: / blobroot**.

      > [!IMPORTANT]
      > Не изменяйте вторую половину значения привязки каталога хранения, которая указывает на определенное расположение в модуле. Привязка каталога хранения всегда должна заканчиваться на **:/blobroot** — для контейнеров Linux и **:C:/BlobRoot** — для контейнеров Windows.

1. Настройка [распределение по уровням](how-to-store-data-blob.md#tiering-properties) и [time-to-live](how-to-store-data-blob.md#time-to-live-properties) свойства для модуля, добавив следующий код JSON для *deployment.template.json* файла. Настройте соответствующее значение каждого свойства и сохраните файл.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }
   ```

   ![задать нужные свойства для azureblobstorageoniotedge - Visual Studio Code](./media/how-to-store-data-blob/tiering_ttl.png)

   Сведения о настройке распределения по уровням и срок ЖИЗНИ, после развертывания модуля, см. в разделе [изменить Двойник модуля](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Дополнительные сведения о контейнере параметры создания, перезапустите политики и требуемого состояния см. в разделе [EdgeAgent требуемые свойства](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Сохраните файл *deployment.template.json*.

1. Щелкните правой кнопкой мыши **deployment.template.json** и выберите **Создать манифест развертывания IoT Edge**.

1. Visual Studio Code принимает информацию, которую вы указали в *deployment.template.json* и использует его для создания файла манифеста развертывания. Манифест развертывания создается в новой папке **config** в рабочей области решения. Получив этот файл, выполните действия, описанные в разделе [Развертывание модулей Azure IoT Edge из Visual Studio Code](how-to-deploy-modules-vscode.md) или [Развертывание модулей IoT Edge Azure с помощью Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Развертывание нескольких экземпляров модуля

Если вы хотите развернуть несколько экземпляров из хранилища BLOB-объектов Azure в модуле IoT Edge, необходимо указать путь хранения и измените `HostPort` значение, которое связывает модуля. Модули службы хранилища BLOB-объектов всегда предоставляют порт 11002 в контейнере, но вы можете объявить, к какому порту он привязан в узле.

Изменить **параметры создания контейнера** (на портале Azure) или **createOptions** поля (в *deployment.template.json* файл в Visual Studio Code) для изменения `HostPort` значение:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

При подключении к дополнительным модулям хранилища BLOB-объектов измените конечную точку, чтобы она указывала на обновленный порт узла.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о работе манифестов развертывания и об их создании см. в руководстве по [использованию, настройке и повторном использовании модулей Azure IoT Edge](module-composition.md).
