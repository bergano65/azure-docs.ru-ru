---
title: Подключение пользовательского контейнера хранилища в Linux
description: Узнайте, как подключить настраиваемую сетевую папку к контейнеру Linux в службе приложений Azure. Совместное использование файлов между приложениями, удаленное управление статическим содержимым и локальный доступ и т. д.
author: msangapu-msft
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 00c60edeefa5fd8d1304aa5fc301a3b0304f5ca3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671793"
---
# <a name="attach-azure-storage-containers-to-linux-containers"></a>Подключение контейнеров службы хранилища Azure к контейнерам Linux

В этом руководство показано, как подключить сетевые общие папки к службе приложений на платформе Linux с помощью службы [хранилища Azure](/azure/storage/common/storage-introduction). К преимуществам относятся защищенное содержимое, переносимость содержимого, постоянное хранилище, доступ к нескольким приложениям и несколько методов передачи.

## <a name="prerequisites"></a>Технические условия

- Существующее веб-приложение (Служба приложений в Linux или Веб-приложение для контейнеров).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 или более поздней версии).

## <a name="create-azure-storage"></a>Создание службы хранилища Azure

> [!NOTE]
> Служба хранилища Azure — это не хранилище по умолчанию, и счета для которого выставляются отдельно. Эта служба не входит в состав веб-приложения.
>
> Не поддерживает использование конфигурации брандмауэра хранилища из-за ограничений инфраструктуры.
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

## <a name="link-storage-to-your-web-app-preview"></a>Связывание хранилища с веб-приложением (предварительная версия)

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
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Использование пользовательского хранилища в Docker Compose

Службу хранилища Azure можно подключить с помощью многоконтейнерных приложений, используя пользовательский идентификатор. Чтобы просмотреть имя настраиваемого идентификатора, выполните [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

В файле *DOCKER-Compose. yml* сопоставьте параметр `volumes` для `custom-id`. Пример.

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка веб-приложений в службе приложений Azure](../configure-common.md).
