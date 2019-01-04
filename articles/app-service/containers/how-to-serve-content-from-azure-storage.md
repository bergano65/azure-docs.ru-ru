---
title: Обработка содержимого из службы хранилища Azure в Службе приложений на платформе Linux
description: Как настраивать и обрабатывать содержимое из службы хранилища Azure в Службе приложений Azure на платформе Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 11/01/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 855df2820db6bba2b47d543ab671bee2193d7d9b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250929"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Обработка содержимого из службы хранилища Azure в Службе приложений на платформе Linux

В этом руководстве показано, как обрабатывать статическое содержимое в Службе приложений на платформе Linux с помощью [службы хранилища Azure](/azure/storage/common/storage-introduction). Преимущества включают защищенное содержимое, его переносимость, доступ к нескольким приложениям и несколько методов для передачи. В этом руководстве вы узнаете, как передавать содержимое в службе хранилища Azure, [настраивая пользовательское хранилище](https://blogs.msdn.microsoft.com/appserviceteam/2018/09/24/announcing-bring-your-own-storage-to-app-service/).

## <a name="prerequisites"></a>Предварительные требования

- Существующее веб-приложение (Служба приложений в Linux или Веб-приложение для контейнеров).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 или более поздней версии).

## <a name="create-azure-storage"></a>Создание службы хранилища Azure

> [!NOTE]
> Служба хранилища Azure — это не хранилище по умолчанию, и счета для которого выставляются отдельно. Эта служба не входит в состав веб-приложения.
>

Создайте [учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Отправка файлов в службу хранилища Azure

Чтобы отправить локальный каталог в учетную запись хранения, используйте команду [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch), как в показано в примере, приведенном ниже.

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app"></a>Связывание хранилища с веб-приложением

> [!CAUTION]
> Связывание существующего каталога в веб-приложении с учетной записью хранения приведет к удалению содержимого каталога. Если в существующем приложении выполняется перенос файлов, перед началом работы создайте резервную копию приложения и его содержимого.
>

Чтобы вставить учетную запись хранения в каталог, в приложении Служба приложений используйте команду [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add). Тип хранилища может быть AzureBlob или AzureFiles. Для этого контейнера используйте AzureBlob.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Для всех остальных каталогов, которые вы хотите связать с учетной записью хранения, следует сделать то же самое.

## <a name="verify"></a>Проверка

Проверить, связан ли контейнер хранилища с веб-приложением, можно, выполнив следующую команду.

```azurecli
az webapp config storage-account list --resource-group <group_name> --name <app_name>
```

## <a name="next-steps"></a>Дополнительная информация

- [Настройка веб-приложений в службе приложений Azure](https://docs.microsoft.com/azure/app-service/web-sites-configure).
