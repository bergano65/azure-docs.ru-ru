---
title: Подавать содержимое от хранилища Azure до контейнеров Linux
description: Узнайте, как прикрепить пользовательскую сеть к контейнеру Linux в службе приложений Azure. Делитесь файлами между приложениями, управляйте статическим содержимым удаленно и доступом локально и т.д.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297923"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Обработка содержимого из службы хранилища Azure в Службе приложений на платформе Linux

> [!NOTE]
> Эта статья относится к контейнерам Linux. Для развертывания в пользовательских [Configure Azure Files in a Windows Container on App Service](../configure-connect-to-azure-storage.md)контейнерах Windows см. Azure Storage in App Service на Linux — это функция **предварительного просмотра.** Эта функция **не поддерживается для производственных сценариев.**
>

В этом руководстве показано, как прикрепить хранилище Azure к службе приложений на Linux. Преимущества включают в себя защищенный контент, переносимость содержимого, постоянное хранение, доступ к нескольким приложениям и несколько методов передачи.

## <a name="prerequisites"></a>Предварительные требования

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 или более поздней версии).
- Существующая [служба приложений на Linux-приложении](https://docs.microsoft.com/azure/app-service/containers/).
- [Учетная запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Общий каталог файлов Azure и каталог](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Ограничения хранения Azure с помощью службы приложений

- Azure Storage с помощью службы приложений находится **в предварительном просмотре** для службы приложений на Linux и Web App для контейнеров. Он не **поддерживается** для **производственных сценариев.**
- Azure Storage с помощью службы app поддерживает установку **контейнеров Azure Files** (Читай/ Запись) и **контейнеров Azure Blob** (только читать)
- Служба хранения azure с помощью службы приложений **не поддерживает** использование конфигурации **хранилища брандмауэра** из-за ограничений инфраструктуры.
- Azure Storage с помощью службы приложений позволяет указать **до пяти** точек крепления в приложении.
- Azure Storage, установленный на приложение, недоступен через конечные точки App Service FTP/FTPs. Используйте [исследователь хранения azure](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage **не включается** в веб-приложение и выставляется отдельно. Узнайте больше о [ценах на хранение Azure.](https://azure.microsoft.com/pricing/details/storage)

> [!WARNING]
> Конфигурации службы приложений с использованием Azure Blob Storage станут прочитанными только в феврале 2020 года. [Дополнительные сведения](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Наверскакните приложение с помощью хранилища Azure

После создания [учетной записи хранения данных Azure, обмена файлами и каталога](#prerequisites)теперь можно настроить приложение с помощью Azure Storage.

Чтобы установить учетную запись хранилища в каталог в [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) приложении Службы приложений, вы используете команду. Тип хранилища может быть AzureBlob или AzureFiles. В этом примере используется AzureFiles.


> [!CAUTION]
> Каталог, указанный как путь крепления в вашем веб-приложении, должен быть пустым. Любой контент, хранящийся в этом каталоге, будет удален при добавлении внешнего крепления. Если в существующем приложении выполняется перенос файлов, перед началом работы создайте резервную копию приложения и его содержимого.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Для всех остальных каталогов, которые вы хотите связать с учетной записью хранения, следует сделать то же самое.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Проверка ссылки на хранение Azure в веб-приложении

Проверить, связан ли контейнер хранилища с веб-приложением, можно, выполнив следующую команду.

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Используйте хранилище Azure в Docker Compose

Хранилище Azure может быть установлено с помощью мультиконтейнерных приложений с использованием пользовательского идентификатора. Чтобы просмотреть имя пользовательского [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)идентификатора, запустите .

В файле *docker-compose.yml* `volumes` отображается `custom-id`опция . Пример:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка веб-приложений в Службе приложений Azure](../configure-common.md).

