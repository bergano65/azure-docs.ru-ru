---
title: Передача данных изображений в облако с помощью службы хранилища Azure | Документация Майкрософт
description: Использование Хранилища BLOB-объектов Azure с веб-приложением для хранения данных приложения
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: c55e63d813c462db8a6c404894b8754f4130d935
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097953"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Руководство. Передача данных изображений в облако с помощью службы хранилища Azure

Это руководство представляет первую часть цикла. Из этого руководства вы узнаете, как развернуть веб-приложение, использующее клиентскую библиотеку службы хранилища Azure, для отправки изображений в учетную запись хранения. По окончании у вас будет веб-приложение, которое хранит и показывает изображения из службы хранилища Azure.

# <a name="nettabnet"></a>[\.NET](#tab/net)
![Представление контейнера изображений](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Представление контейнера изображений](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

В первой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание учетной записи хранения
> * Создание контейнера и настройка разрешений.
> * Получение ключа доступа.
> * Развертывание веб-приложения в Azure.
> * Настройка параметров приложения
> * Взаимодействие с веб-приложением.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется подписка Azure. Перед началом работы создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать интерфейс командной строки локально, это руководство требует запустить Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Создание группы ресурсов 

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.  

В следующем примере создается группа ресурсов `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

В примере изображения отправляются в контейнер больших двоичных объектов в учетной записи хранения Azure. Учетная запись хранения предоставляет уникальное пространство имен для хранения ваших объектов данных в службе хранилища Azure и доступа к ним. Создайте учетную запись хранения в созданной вами группе ресурсов с помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create).

> [!IMPORTANT]
> Во второй части этого руководства используется Сетка событий Azure для хранилища BLOB-объектов. Убедитесь в том, что созданная учетная запись хранения в регионе Azure поддерживает Сетку событий. Список поддерживаемых регионов см. в разделе [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

В следующей команде замените глобально уникальное имя учетной записи хранения больших двоичных объектов там, где встречается заполнитель `<blob_storage_account>`.  

```azurecli-interactive
az storage account create --name <blob_storage_account> \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>Создание контейнеров хранилища BLOB-объектов

Приложение использует два типа контейнеров в учетной записи хранилища больших двоичных объектов. Контейнеры похожи на папки и используются для хранения больших двоичных объектов. Контейнер *изображений* — это контейнер, в который отправляются изображения с высоким разрешением. В одной из последующих статей этой серии приложение-функция Azure будет отправлять эскизы изображений измененного размера в контейнер *эскизов*.

Получите ключи своей учетной записи хранения с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list). Затем используйте этот ключ для создания двух контейнеров с помощью команды [az storage container create](/cli/azure/storage/container#az_storage_container_create).  

В данном случае `<blob_storage_account>` — это имя созданной учетной записи хранения больших двоичных объектов. Общему доступу к контейнеру *Изображения* присваивается значение `off`. Общему доступу к контейнеру *Эскизы* присваивается значение `container`. Параметр общего доступа `container` позволяет пользователям веб-страницы просматривать эскизы.

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Запишите имя и ключ учетной записи хранения больших двоичных объектов. В этом примере приложения эти параметры используются для подключения к учетной записи хранения и отправки изображений. 

## <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[План службы приложений](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) указывает расположение, размер и функции фермы веб-серверов, в которой размещается приложение.

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create).

В следующем примере создается план службы приложений с именем `myAppServicePlan` и ценовой категорией **Бесплатный**.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Создание веб-приложения

Веб-приложение предоставляет пространство для размещения примера кода приложения, развернутого из примера репозитория GitHub. Создайте [веб-приложение](../../app-service/app-service-web-overview.md) в рамках плана `myAppServicePlan` службы приложений с помощью команды [az webapp create](/cli/azure/webapp#az_webapp_create).  

В следующей команде замените `<web_app>` уникальным именем. Допустимые символы: `a-z`, `0-9` и `-`. Если `<web_app>` не является уникальным, отобразится сообщение об ошибке: _Веб-сайт с именем `<web_app>` уже существует._ URL-адрес приложения по умолчанию: `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Развертывание примера приложения из репозитория GitHub

# <a name="nettabnet"></a>[\.NET](#tab/net)

Служба приложений поддерживает несколько способов развертывания содержимого для веб-приложения. В этом руководстве развертывается веб-приложение из [общедоступного репозитория примеров GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Настройте для веб-приложения развертывание GitHub с помощью команды [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Замените `<web_app>` именем веб-приложения, созданного на предыдущем шаге.

Пример проекта содержит приложение [ASP.NET MVC](https://www.asp.net/mvc). Оно принимает изображение, сохраняет его в учетную запись хранения и показывает изображения из контейнера эскизов. Веб-приложение использует пространства имен [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) и [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) из клиентской библиотеки службы хранилища Azure для взаимодействия со службой хранилища Azure.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
Служба приложений поддерживает несколько способов развертывания содержимого для веб-приложения. В этом руководстве развертывается веб-приложение из [общедоступного репозитория примеров GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Настройте для веб-приложения развертывание GitHub с помощью команды [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Замените `<web_app>` именем веб-приложения, созданного на предыдущем шаге.

---

```azurecli-interactive
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

## <a name="configure-web-app-settings"></a>Настройка параметров веб-приложения

В примере приложения [клиентская библиотека хранилища Azure](/dotnet/api/overview/azure/storage?view=azure-dotnet) используется для запроса маркеров доступа, которые используются для отправки изображений. Учетные данные записи хранения, используемые пакетом SDK службы хранилища, задаются в параметрах приложения для веб-приложения. Добавить параметры в развернутое приложение можно с помощью команды [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

В следующей команде замените `<blob_storage_account>` именем учетной записи хранения больших двоичных объектов, а `<blob_storage_key>` — связанным ключом. Замените `<web_app>` именем веб-приложения, созданного на предыдущем шаге.

```azurecli-interactive
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
```

После развертывания и настройки веб-приложения можно проверить возможность отправки изображений в приложении.

## <a name="upload-an-image"></a>Передача образа

Чтобы проверить веб-приложение, перейдите по URL-адресу опубликованного приложения. URL-адрес приложения по умолчанию: `https://<web_app>.azurewebsites.net`.
Выберите область **Отправка фотографий**, чтобы выбрать и отправить файл, или перетащите файлы в нее. При успешной отправке изображение исчезнет.

# <a name="nettabnet"></a>[\.NET](#tab/net)

![Приложение ImageResizer](media/storage-upload-process-images/figure1.png)

В примере кода задача `UploadFiletoStorage` в файле *Storagehelper.cs* используется для отправки изображений в контейнер *Изображения* в учетной записи хранения с помощью метода [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). В следующем примере кода содержится задача `UploadFiletoStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

В предыдущей задаче используются следующие классы и методы:

|Класс  |Метод  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

![Приложение для оправки изображений](media/storage-upload-process-images/upload-app-nodejs.png)

В примере кода маршрут `post` отвечает за отправку изображения в контейнер BLOB-объектов. Маршрут использует модули для обработки отправки:

- [multer](https://github.com/expressjs/multer) реализует стратегию отправки для обработчика маршрутов.
- [into-stream](https://github.com/sindresorhus/into-stream) преобразует буфер в поток, как требует [createBlockBlobFromStream]. http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream)

Когда файл отправляется в маршрут, содержимое файла остается в памяти до тех пор, пока файл не будет загружен в контейнер BLOB-объектов.

> [!IMPORTANT]
> Загрузка очень больших файлов в память может негативно повлиять на производительность вашего веб-приложения. Если вы ожидаете, что пользователи опубликуют большие файлы, можете просмотреть промежуточные файлы в файловой системе веб-сервера, а затем планировать загрузку в хранилище BLOB-объектов. После того как файлы попадают в хранилище BLOB-объектов, вы можете удалить их из файловой системы сервера.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Проверка отображения изображения в учетной записи хранения

Войдите на [портале Azure](https://portal.azure.com). В меню слева выберите **Учетные записи хранения**, а затем имя своей учетной записи хранения. В разделе **Служба BLOB-объектов** выберите **большие двоичные объекты**, а затем выберите контейнер **Изображения**.

Убедитесь, что изображение отображается в контейнере.

![Представление контейнера изображений](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Тестирование просмотра эскизов

Для проверки просмотра эскизов отправьте изображение в контейнер **Эскизы**, чтобы убедиться, что приложение может считывать контейнер **Эскизы**.

Войдите на [портале Azure](https://portal.azure.com). В меню слева выберите **Учетные записи хранения**, а затем имя своей учетной записи хранения. В разделе **Служба BLOB-объектов** выберите **большие двоичные объекты**, а затем выберите контейнер **Эскизы**. Щелкните **Отправить**, чтобы открыть область **Передать BLOB-объект**.

С помощью средства выбора файлов выберите файл и щелкните **Отправить**.

Вернитесь к своему приложению и убедитесь, что изображение, отправленное в контейнер **эскизов**, отображается.

# <a name="nettabnet"></a>[\.NET](#tab/net)
![Представление контейнера изображений](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Представление контейнера изображений](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Во второй части серии вы автоматизируете создание эскизных изображений, что позволит обойтись без этого изображения. В контейнере **эскизов** на портале Azure выберите отправленное изображение и щелкните **Удалить**, чтобы удалить его. 

CDN можно включить для кэширования содержимого из учетной записи хранения Azure. Дополнительные сведения о том, как включить CDN с учетной записью хранения Azure, см. в статье [Краткое руководство по интеграции учетной записи хранения Azure с Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Дополнительная информация

В первой части серии вы узнали, как настроить веб-приложение для взаимодействия с хранилищем.

Перейдите ко второй части руководства, чтобы узнать об использовании Сетки событий для активации функции Azure, изменяющей размер изображения.

> [!div class="nextstepaction"]
> [Automate resizing uploaded images using Event Grid](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Автоматическое изменение размера отправленных изображений с помощью службы "Сетка событий")
