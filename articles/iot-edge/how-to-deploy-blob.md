---
title: Развертывание хранилища капли на модуле для вашего устройства - Azure IoT Edge
description: Разверните модуль хранилища BLOB-объектов Azure на вашем устройстве IoT Edge для хранения данных на границе.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 04b145622a1a4237b576a1bb512b5f749f9c3823
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133331"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Развертывание хранилища BLOB-объектов Azure в модуле IoT Edge на устройстве

Существует несколько способов развертывания модулей на устройстве IoT Edge, и все они работают для azure Blob Storage на модулях IoT Edge. Два простейших метода — это портал Azure или шаблоны Visual Studio Code.

## <a name="prerequisites"></a>Предварительные требования

- [Концентратор IoT](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.
- [Устройство IoT Edge](how-to-register-device.md) с установленной средой выполнения IoT Edge.
- [Визуальный студийный код](https://code.visualstudio.com/) и [инструменты Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) при развертывании из Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Развертывание с портала Azure

Портал Azure поможет вам создать манифест развертывания и довести развертывание до устройства IoT Edge.

### <a name="select-your-device"></a>Выбор устройства

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к концентратору IoT.
1. В меню выберите **IoT Edge**.
1. Щелкните идентификатор целевого устройства в списке устройств.
1. Выберите **набор модулей.**

### <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. На портале Azure есть мастер, который позволяет создать манифест развертывания. Он имеет три шага, организованные в вкладки: **Модули**, **маршруты**, и **Обзор и создать**.

#### <a name="add-modules"></a>Добавление модулей

1. В разделе **Модулей IoT Edge На** странице щелкните отсечения **И** добавить и выберите **модуль IoT Edge** для отображения страницы **Модуля Add IoT Edge.**

2. На вкладке **Настройки модуля** укажите имя модуля, а затем укажите изображение контейнера URI:

   Примеры:
  
   - **Название модуля IoT Edge**:`azureblobstorageoniotedge`
   - **Изображение URI**:`mcr.microsoft.com/azure-blob-storage:latest`

   ![Настройки модуля-близнецы](./media/how-to-deploy-blob/addmodule-tab1.png)

   Не выбирайте **Добавить,** пока вы не указали значения на **параметры модуля,** **параметры создания контейнеров**, и **Модуль Твин Настройки** вкладок, как описано в этой процедуре.

   > [!IMPORTANT]
   > Azure IoT Edge является чувствительным к случаям при выполнении вызовов модулей, а SDK хранилища также по умолчанию по умолчанию. Хотя название модуля в [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) — **AzureBlobStorageonIoTEdge,** изменение имени на нижний регистр помогает гарантировать, что подключение к модулю Azure Blob Storage на модуле IoT Edge не прерывается.

3. Откройте вкладку **«Параметры создания контейнеров».**

   ![Настройки модуля-близнецы](./media/how-to-deploy-blob/addmodule-tab3.png)

   Копировать и вставлять следующие JSON в поле, чтобы обеспечить информацию о счете хранения и крепления для хранения на вашем устройстве.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
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

4. Обновление JSON, которое вы скопировали в **параметры создания контейнеров** со следующей информацией:

   - Замените `<your storage account name>` запоминаемым именем. Имена учетных записей должны быть от 3 до 24 символов в длину, с нижними буквами и цифрами. Нельзя использовать пробелы.

   - Замените `<your storage account key>` на 64-байтовый ключ Base64. Вы можете создать ключ с помощью таких средств, как [GeneratePlus](https://generate.plus/en/base64). Вы будете использовать эти учетные данные для доступа к хранилищу BLOB-объектов из других модулей.

   - Заменить `<storage mount>` в соответствии с контейнерной операционной системы. Укажите имя [тома](https://docs.docker.com/storage/volumes/) или абсолютный путь к существующему каталогу на устройстве IoT Edge, где модуль blob будет хранить свои данные. Установка хранилища отображает местоположение на устройстве, которое предоставляется в заданным местоположением в модуле.

     - Для контейнеров Linux формат — * \<это путь хранения или объем>:/blobroot.* Например.
         - использовать [объем крепления](https://docs.docker.com/storage/volumes/): мой **объем:/blobroot**
         - использовать [связывать крепление](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Убедитесь в том, чтобы следовать шагам, чтобы [предоставить доступ к каталогу для пользователя контейнера](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Для контейнеров Windows формат — * \<это путь хранения или объем>:C:/BlobRoot.* Например.
         - использовать [объем крепления](https://docs.docker.com/storage/volumes/): мой **объем: C: / blobroot**.
         - использовать [крепление :](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - Вместо того, чтобы использовать локальный диск, вы можете сопоставить местоположение сети SMB, для получения дополнительной информации, [увидеть с помощью SMB доля как локальное хранилище](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Не изменяйте значение крепления второй половины хранилища, которое указывает на определенное место в модуле. Монтировка для хранения всегда должна **заканчиваться:/blobroot** для контейнеров Linux **и:C:/BlobRoot** для контейнеров Windows.

5. На вкладке **Параметры Модуля Twin Настройки** скопируйте следующий JSON и вставьте его в поле.

   ![Настройки модуля-близнецы](./media/how-to-deploy-blob/addmodule-tab4.png)

   Налажить каждое свойство с соответствующей стоимостью, как указано заполнителями. Если вы используете симулятор IoT Edge, установите значения для соответствующих переменных среды для этих свойств, описанных [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) и [deviceAutoDeleteProperties.](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Для получения информации о настройке устройстваToCloudUploadProperties и deviceAutoDeleteProperties [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)после развертывания модуля см. Для получения дополнительной информации о желаемых свойствах [см.](module-composition.md#define-or-update-desired-properties)

6. Нажмите кнопку **Добавить**.

7. Выберите **Далее: Маршруты** для продолжения раздела маршрутов.

#### <a name="specify-routes"></a>Настройка маршрутов

Сохраняйте маршруты по умолчанию и выберите **Далее: Обзор и создайте,** чтобы продолжить раздел обзора.

#### <a name="review-deployment"></a>Проверка развертывания

В разделе обзорной информации вы увидите манифест развертывания в формате JSON, который был создан на основе параметров, выбранных вами в двух предыдущих разделах. Есть также два модуля заявил, что вы не добавить: **$edgeAgent** и **$edgeHub**. Эти два модуля составляют [среду выполнения IoT Edge](iot-edge-runtime.md) и являются обязательными для каждого развертывания.

Просмотрите информацию о развертывании, а затем выберите **Create**.

### <a name="verify-your-deployment"></a>Проверка развертывания

После создания развертывания вы возвращаетесь на страницу **IoT Edge** концентратора IoT.

1. Выберите устройство IoT Edge, на которое отправили развертывание, чтобы открыть сведения о нем.
1. В сведениях об устройстве убедитесь, что для модуля службы хранилища BLOB-объектов настроены параметры **Указано в развертывании** и **Данные, полученные от устройства**.

Запуск модуля на устройстве может занять несколько секунд. Затем информация о нем передается в Центр Интернета вещей. Обновите страницу, чтобы увидеть обновленное состояние.

## <a name="deploy-from-visual-studio-code"></a>Развертывание из кода visual Studio

Azure IoT Edge предоставляет шаблоны в Visual Studio Code для разработки решений для границы. Используйте следующие шаги для создания нового решения IoT Edge с модулем хранения кабы и настройки манифеста развертывания.

1. Выберите**палитру команды** **просмотра.** > 

1. В палитре команд введите и запустите команду **Azure IoT Edge: Новое решение IoT Edge.**

   ![Запуск команды создания решения IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Следуйте инструкциям на экране в палитре команд для создания решения.

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Для создания файлов решений выберите место на машине разработки для Visual Studio Code. |
   | Введите название решения. | Введите описательное имя решения или примите имя по умолчанию **EdgeSolution**. |
   | Выбор шаблона модуля | Выберите **Existing Module (Enter full image URL)** (Имеющийся модуль (введите полный URL-адрес образа)). |
   | Указание имени модуля | Введите имя все-нижнего регистра для вашего модуля, как **azureblobstorageoniotedge**.<br/><br/>В модуле IoT Edge очень важно использовать знаки нижнего регистра для имени хранилища BLOB-объектов Azure. IoT Edge учитывает регистр при ссылке на модули, а название пакета SDK службы хранилища по умолчанию преобразовывается в нижний регистр. |
   | Указание образа Docker для модуля | Укажите универсальный код ресурса (URI) образа: **mcr.microsoft.com/azure-blob-storage:latest**. |

   Visual Studio Code принимает предоставленные сведения, создает решение IoT Edge, а затем загружает его в новом окне. Шаблон решения создает шаблон манифеста развертывания, который содержит образ модуля хранилища BLOB-объектов, но вам нужно настроить параметры создания модуля.

1. Откройте *deployment.template.json* в рабочей области нового решения и найдите раздел **modules**. Внесите следующие изменения конфигурации.

   1. Удалите модуль **SimulatedTemperatureSensor,** так как это не обязательно для этого развертывания.

   1. Копировать и вставить следующий `createOptions` код в поле:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Обновление модуля createOptions - Визуальный код студии](./media/how-to-deploy-blob/create-options.png)

1. Замените `<your storage account name>` запоминаемым именем. Имена учетных записей должны быть от 3 до 24 символов в длину, с нижними буквами и цифрами. Нельзя использовать пробелы.

1. Замените `<your storage account key>` на 64-байтовый ключ Base64. Вы можете создать ключ с помощью таких средств, как [GeneratePlus](https://generate.plus/en/base64). Вы будете использовать эти учетные данные для доступа к хранилищу BLOB-объектов из других модулей.

1. Заменить `<storage mount>` в соответствии с контейнерной операционной системы. Укажите имя [тома](https://docs.docker.com/storage/volumes/) или абсолютный путь к каталогу на устройстве IoT Edge, где модуль BLOB-объектов должен хранить данные. Установка хранилища отображает местоположение на устройстве, которое предоставляется в заданным местоположением в модуле.  

     - Для контейнеров Linux формат — * \<это путь хранения или объем>:/blobroot.* Например.
         - использовать [объем крепления](https://docs.docker.com/storage/volumes/): мой **объем:/blobroot**
         - использовать [связывать крепление](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Убедитесь в том, чтобы следовать шагам, чтобы [предоставить доступ к каталогу для пользователя контейнера](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Для контейнеров Windows формат — * \<это путь хранения или объем>:C:/BlobRoot.* Например.
         - использовать [объем крепления](https://docs.docker.com/storage/volumes/): мой **объем: C: / blobroot**.
         - использовать [крепление :](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - Вместо того, чтобы использовать локальный диск, вы можете сопоставить местоположение сети SMB, для получения дополнительной информации [см. с помощью sMB share в качестве локального хранилища](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Не изменяйте значение крепления второй половины хранилища, которое указывает на определенное место в модуле. Монтировка для хранения всегда должна **заканчиваться:/blobroot** для контейнеров Linux **и:C:/BlobRoot** для контейнеров Windows.

1. Назначьте [устройствоToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) и [устройствоAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) для вашего модуля, добавив следующий JSON в файл *deployment.template.json.* Налажить каждое свойство с соответствующим значением и сохранить файл. Если вы используете симулятор IoT Edge, установите значения для соответствующих переменных среды для этих свойств, которые вы можете найти в разделе объяснения [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) и [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![набор желаемых свойств для azureblobstorageoniotedge - Визуальный код студии](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Для получения информации о настройке устройстваToCloudUploadProperties и deviceAutoDeleteProperties [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)после развертывания модуля см. Для получения дополнительной информации о вариантах создания контейнера, политике перезагрузки и желаемом статусе [см.](module-edgeagent-edgehub.md#edgeagent-desired-properties)

1. Сохранить файл *deployment.template.json.*

1. Щелкните правой кнопкой мыши **deployment.template.json** и выберите **Создать манифест развертывания IoT Edge**.

1. Visual Studio Code использует предоставленную вами информацию в *deployment.template.json* и использует ее для создания нового файла манифеста развертывания. Манифест развертывания создается в новой папке **config** в рабочей области решения. Получив этот файл, выполните действия, описанные в разделе [Развертывание модулей Azure IoT Edge из Visual Studio Code](how-to-deploy-modules-vscode.md) или [Развертывание модулей IoT Edge Azure с помощью Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Развертывание нескольких экземпляров модуля

Если требуется развернуть несколько экземпляров хранилища Azure Blob на модуле IoT Edge, необходимо предоставить другой путь хранения и изменить `HostPort` значение, к которому модуль привязывается. Модули службы хранилища BLOB-объектов всегда предоставляют порт 11002 в контейнере, но вы можете объявить, к какому порту он привязан в узле.

Изменить **параметры создания контейнеров** (на портале Azure) или поле **createOptions** (в файле *deployment.template.json* в коде Visual Studio) для изменения `HostPort` значения:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

При подключении к дополнительным модулям хранилища BLOB-объектов измените конечную точку, чтобы она указывала на обновленный порт узла.

## <a name="configure-proxy-support"></a>Настройка поддержки прокси-сервера

Если ваша организация использует прокси-сервер, вам нужно настроить прокси-поддержку для модулей edgeAgent и edgeHub. Этот процесс включает в себя две задачи:

- Нанастройка на устройство наустройство для деймонов времени выполнения и агента IoT Edge.
- Установите переменную среды HTTPS_PROXY для модулей в развертывании манифеста JSON.

Этот процесс описан в [настройке устройства IoT Edge для связи через прокси-сервер.](how-to-configure-proxy-support.md)

Кроме того, модуль хранения кабр также требует HTTPS_PROXY настройки в файле развертывания явного. Можно непосредственно отсеить файл манифеста развертывания или использовать портал Azure.

1. Перейдите к концентратору Iot на портале Azure и выберите **Iot Edge** из меню левого стекла.

1. Выберите устройство с модулем для настройки.

1. Выберите **набор модулей.**

1. В разделе **Модулей IoT Edge на** странице выберите модуль хранения кабл.

1. На странице **модуля Обновления IoT Edge** выберите вкладку **«Переменные среды».**

1. Добавьте `HTTPS_PROXY` имя **и** URL-адрес прокси для **значения.**

      ![Установка HTTPS_PROXY переменной среды](./media/how-to-deploy-blob/https-proxy-config.png)

1. Нажмите **обновление**, затем **Просмотрите и создайте**.

1. Обратите внимание, что прокси добавляется в модуль в развертывании манифеста и выберите **Создать.**

1. Проверьте настройку, выбрав модуль со страницы данных об устройстве, а в нижней части **страницы Модулей IoT Edge** Выберите вкладку **«Переменные среды».**

      ![Установка HTTPS_PROXY переменной среды](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [хранилище Azure Blob на IoT Edge.](how-to-store-data-blob.md)

Дополнительные сведения о работе манифестов развертывания и об их создании см. в руководстве по [использованию, настройке и повторном использовании модулей Azure IoT Edge](module-composition.md).
