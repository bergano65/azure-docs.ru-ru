---
title: Использование службы "Сетка событий Azure" для автоматического изменения размера переданных изображений | Документация Майкрософт
description: Служба "Сетка событий Azure" может быть активирована при передаче больших двоичных объектов в службу хранилища Azure. Это можно использовать для отправки файлов изображений, которые передаются в службу хранилища Azure, в другие службы, например службу "Функции Azure", для изменения размера и других улучшений.
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 919bd20bc5650bc2a843680770fad6190ef01361
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182342"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Руководство. Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"

[Сетка событий Azure](overview.md) — это служба обработки событий для облака. Служба "Сетка событий" дает возможность создавать подписки на события, порождаемые службами Azure или сторонними ресурсами.  

Это руководство — вторая часть из цикла руководств по службе хранилища. Оно дополняет [предыдущее руководство по службе хранилища][previous-tutorial] и содержит сведения о том, как добавить автоматическое бессерверное создание эскизов с помощью служб "Функции Azure" и "Сетка событий Azure". Служба "Сетка событий" позволяет службе [Функции Azure](../azure-functions/functions-overview.md) реагировать на события [хранилища BLOB-объектов Azure](../storage/blobs/storage-blobs-introduction.md) и создавать эскизы передаваемых изображений. Подписка на событие создается для события создания хранилища BLOB-объектов. При добавлении большого двоичного объекта в конкретный контейнер хранилища BLOB-объектов вызывается конечная точка функции. Данные, передаваемые посредством привязки функции из службы "Сетка событий", используются для доступа к большому двоичному объекту и создания эскизов.

Для добавления возможностей изменения размера в существующее приложение для передачи изображений можно использовать Azure CLI и портал Azure.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

![Опубликованное веб-приложение в браузере](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Пакет SDK для Node.js версии 2](#tab/nodejs)

![Опубликованное веб-приложение в браузере](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Пакет SDK для Node.js версии 10](#tab/nodejsv10)

![Опубликованное веб-приложение в браузере](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание общей учетной записи хранения Azure;
> * развертывание бессерверного кода с помощью службы "Функции Azure";
> * создание подписки на событие хранилища BLOB-объектов в службе "Сетка событий".

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Для работы с этим руководством:

Необходимо изучить руководство по использованию хранилища BLOB-объектов [Передача данных изображений в облако с помощью службы хранилища Azure][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Зарегистрируйте поставщик ресурсов службы "Сетка событий" в подписке, если вы еще не сделали это.

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.14 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Если вы не используете Cloud Shell, сначала выполните вход с помощью `az login`.

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

Для службы "Функции Azure" требуется общая учетная запись хранения. Кроме учетной записи хранения BLOB-объектов, созданной в рамках предыдущего руководства, создайте отдельно общую учетную запись хранения в группе ресурсов с помощью команды [az storage account create](/cli/azure/storage/account). Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв. 

1. Задайте переменную для хранения имени группы ресурсов, созданную при работе с предыдущим руководством. 

    ```azurecli-interactive
    resourceGroupName=myResourceGroup
    ```
2. Задайте переменную имени новой учетной записи хранения, которая требуется для приложения-функции Azure. 

    ```azurecli-interactive
    functionstorage=<name of the storage account to be used by the function>
    ```
3. Создайте учетную запись хранения для функции Azure. 

    ```azurecli-interactive
    az storage account create --name $functionstorage --location southeastasia \
    --resource-group $resourceGroupName --sku Standard_LRS --kind storage
    ```

## <a name="create-a-function-app"></a>Создание приложения-функции  

Для выполнения функций вам понадобится приложение-функция, предоставляющее среду для выполнения кода функции без сервера. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp). 

В следующей команде укажите уникальное название приложения-функции. Имя приложения-функции используется по умолчанию в качестве его домена DNS. Поэтому оно должно быть уникальным среди всех приложений в Azure. 

1. Укажите имя для создаваемого приложения-функции. 

    ```azurecli-interactive
    functionapp=<name of the function app>
    ```
2. Создайте функцию Azure. 

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage \
    --resource-group $resourceGroupName --consumption-plan-location southeastasia
    ```

Теперь необходимо настроить приложение функцию для подключения к учетной записи хранения, созданной в предыдущем [руководстве][previous-tutorial].

## <a name="configure-the-function-app"></a>Настройка приложения-функции

Чтобы обеспечить работу приложения-функции, введите данные входа в учетную запись хранения BLOB-объектов, добавив эти данные в настройки приложения-функции с помощью команды [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Пакет SDK для Node.js версии 2](#tab/nodejs)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString \
THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Пакет SDK для Node.js версии 10](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

Параметр `FUNCTIONS_EXTENSION_VERSION=~2` позволяет запустить приложение-функцию в версии 2 среды выполнения решения "Функции Azure".

Теперь можно развернуть проект кода функции в этом приложении-функции.

## <a name="deploy-the-function-code"></a>Развертывание кода функции 

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Пример функции изменения размера на C# см. на [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Разверните этот код в приложение-функцию с помощью команды [az functionapp deployment source config](/cli/azure/functionapp/deployment/source). 

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName --branch master --manual-integration --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Пакет SDK для Node.js версии 2](#tab/nodejs)

Пример функции изменения размера на Node.js можно найти в [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node). Разверните этот проект кода функции в приложение-функцию с помощью команды [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
--resource-group $resourceGroupName --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Пакет SDK для Node.js версии 10](#tab/nodejsv10)

Пример функции изменения размера на Node.js можно найти в [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Разверните этот проект кода функции в приложение-функцию с помощью команды [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
--resource-group $resourceGroupName --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```
---

Функция изменения размера образа активируется HTTP-запросом, отправленным из службы "Сетка событий". Вы указываете этой службе, что хотите получать эти уведомления по URL-адресу функции, создав подписку на события. В этом руководстве вы подписываетесь на события, созданные большим двоичным объектом.

Данные, передаваемые в функцию из уведомления службы "Сетка событий", содержат URL-адрес большого двоичного объекта. Этот URL-адрес передается входной привязке, что позволяет получить отправленный образ из хранилища BLOB-объектов. Функция создает эскиз и записывает результирующий поток в отдельный контейнер в хранилище BLOB-объектов. 

Этот проект использует `EventGridTrigger` как тип триггера. Рекомендуется использовать триггер Сетки событий, а не универсальные триггеры HTTP. Сетка событий автоматически проверяет триггеры функций Сетки событий. При использовании универсальных триггеров HTTP вам нужно реализовать [ответ проверки](security-authentication.md).

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Дополнительные сведения об этой функции см. в [файлах function.json и run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v2-sdktabnodejs"></a>[Пакет SDK для Node.js версии 2](#tab/nodejs)

См. дополнительные сведения об этой функции в [файлах function.json и index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node/tree/master/Thumbnail).

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Пакет SDK для Node.js версии 10](#tab/nodejsv10)

См. дополнительные сведения об этой функции в [файлах function.json и index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

Код проекта функция развертывается непосредственно из общедоступного примера репозитория. Чтобы узнать больше о параметрах развертывания службы "Функции Azure", ознакомьтесь с разделом [Непрерывное развертывание для Функций Azure](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Создание подписки на событие

Подписка на событие определяет, какие создаваемые поставщиком события необходимо отправлять в конкретную конечную точку. В данном случае конечная точка предоставляется функцией. Чтобы создать подписку на событие, которая отправляет уведомления в функцию, выполните следующие действия на портале Azure: 

1. На [портале Azure](https://portal.azure.com) выберите **Все службы** в меню слева и щелкните **Приложения-функции**. 

    ![Переход к приложениям-функциям на портале Azure](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Разверните приложение-функцию, выберите функцию **Эскиз** и щелкните **Добавить подписку Сетки событий**.

    ![Переход к приложениям-функциям на портале Azure](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Задайте значения параметров подписки на событие, указанные в таблице.
    
    ![Создание подписки на событие из функции на портале Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя** | imageresizersub | Имя, которое идентифицирует новую подписку на событие. | 
    | **Тип раздела** |  учетные записи хранения; | Выберите поставщик событий учетной записи хранения. | 
    | **Подписка** | Ваша подписка Azure. | По умолчанию должна быть выбрана ваша текущая подписка Azure.   |
    | **Группа ресурсов** | myResourceGroup | Щелкните **Использовать существующую** и выберите группу ресурсов, используемую в этом руководстве.  |
    | **Ресурс** |  Учетная запись хранения больших двоичных объектов |  Выберите учетную запись хранения BLOB-объектов, созданную вами. |
    | **Типы событий** | Blob created | Снимите флажки всех типов, кроме **Blob created** (Большой двоичный объект создан). Только события типа `Microsoft.Storage.BlobCreated` будут передаваться в функцию.| 
    | **Тип подписчика** |  autogenerated |  Предварительно определен как веб-перехватчик. |
    | **Конечная точка подписчика** | autogenerated | Используйте URL-адрес конечной точки, созданный автоматически. | 
4. Перейдите на вкладку **Фильтр** и выполните следующие действия:     
    1. Выберите параметр **Включить фильтрацию тем**.
    2. Для **Тема начинается с** укажите следующее значение: **/blobServices/default/containers/images/blobs/**.

        ![Выбор фильтра для подписки на событие](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png) 
2. Нажмите кнопку **Создать**, чтобы добавить подписку на событие. Создается подписка на событие, которая вызывает функцию `Thumbnail` при добавлении большого двоичного объекта в контейнер `images`. Эта функция изменяет размер изображений и добавляет их в контейнер `thumbnails`.

Теперь, когда внутренние службы настроены, следует проверить функциональные возможности изменения размера изображений в примере веб-приложения. 

## <a name="test-the-sample-app"></a>Поверка примера приложения

Чтобы проверить изменение размера изображений в веб-приложении, перейдите на URL-адрес опубликованного приложения. URL-адрес приложения по умолчанию: `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Щелкните область **Upload photos** (Передача фотографий), чтобы выбрать и передать файл. Можно также перетащить фотографию в эту область. 

Обратите внимание, что после того, как переданное изображение исчезнет, его копия появится в карусели **Generated thumbnails** (Созданные эскизы). С помощью функции размер этого образа был изменен, после чего он был добавлен в контейнер *эскизов* и скачан веб-клиентом.

![Опубликованное веб-приложение в браузере](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Пакет SDK для Node.js версии 2](#tab/nodejs)

Чтобы выбрать файл, щелкните **Выбрать файл**, а затем — **Загрузить изображение**. Если изображение загрузилось, браузер направит вас к странице успешной загрузки. Щелкните ссылку, чтобы вернуться к начальной странице. Копия отправленного изображения отображается в области **Generated thumbnails** (Созданные эскизы). (Если изображение не появилось сразу, попробуйте перезагрузить страницу.) С помощью функции размер этого образа был изменен, после чего он был добавлен в контейнер *эскизов* и скачан веб-клиентом.

![Опубликованное веб-приложение в браузере](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Пакет SDK для Node.js версии 10](#tab/nodejsv10)

Чтобы выбрать файл, щелкните **Выбрать файл**, а затем — **Загрузить изображение**. Если изображение загрузилось, браузер направит вас к странице успешной загрузки. Щелкните ссылку, чтобы вернуться к начальной странице. Копия отправленного изображения отображается в области **Generated thumbnails** (Созданные эскизы). (Если изображение не появилось сразу, попробуйте перезагрузить страницу.) С помощью функции размер этого образа был изменен, после чего он был добавлен в контейнер *эскизов* и скачан веб-клиентом.

![Опубликованное веб-приложение в браузере](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * создание общей учетной записи хранения Azure;
> * развертывание бессерверного кода с помощью службы "Функции Azure";
> * создание подписки на событие хранилища BLOB-объектов в службе "Сетка событий".

Перейдите к третьей части цикла руководств по службе хранилища, чтобы узнать, как защитить доступ к учетной записи хранения.

> [!div class="nextstepaction"]
> [Безопасный доступ к данным приложения в облаке](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Чтобы узнать больше о службе "Сетка событий", ознакомьтесь с разделом [Общие сведения о службе "Сетка событий Azure"](overview.md). 
+ Чтобы ознакомиться с еще одним руководством по возможностям службы "Функции Azure", прочитайте в статью [Создание функции, интегрируемой с Azure Logic Apps](../azure-functions/functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
