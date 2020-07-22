---
title: Обслуживание содержимого из службы хранилища Azure в контейнеры Linux
description: Узнайте, как подключить настраиваемую сетевую папку к контейнеру Linux в службе приложений Azure. Совместное использование файлов между приложениями, удаленное управление статическим содержимым и локальный доступ и т. д.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 9a5a38ea32d927f50fb9ddbebe3e1c3533e6fcc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82625329"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Обработка содержимого из службы хранилища Azure в Службе приложений на платформе Linux

> [!NOTE]
> Эта статья относится к контейнерам Linux. Сведения о развертывании в пользовательских контейнерах Windows см. в статье [Настройка файлов Azure в контейнере Windows в службе приложений](../configure-connect-to-azure-storage.md). Служба хранилища Azure в службе приложений на платформе Linux — это **Предварительная версия** функции. Эта функция **не поддерживается в рабочих сценариях**.
>

В этом руководство показано, как подключить службу хранилища Azure к службе приложений на платформе Linux. К преимуществам относятся защищенное содержимое, переносимость содержимого, постоянное хранилище, доступ к нескольким приложениям и несколько методов передачи.

## <a name="prerequisites"></a>Предварительные требования

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 или более поздней версии).
- Существующая [служба приложений в приложении Linux](https://docs.microsoft.com/azure/app-service/containers/).
- [Учетная запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli) ;
- [Общая папка и каталог Azure](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Ограничения службы хранилища Azure со службой приложений

- Служба хранилища Azure со службой приложений доступна **в предварительной версии** для службы приложений на платформе Linux и веб-приложение для контейнеров. Он **не поддерживается** в **рабочих сценариях**.
- Служба хранилища Azure со службой приложений поддерживает подключение **контейнеров файлов Azure** (чтение и запись) и **контейнеров больших двоичных объектов Azure** (только для чтения).
- Служба хранилища Azure со службой приложений **не поддерживает** использование конфигурации **брандмауэра хранилища** из-за ограничений инфраструктуры.
- Служба хранилища Azure с помощью службы приложений позволяет указать до **пяти** точек подключения для каждого приложения.
- Служба хранилища Azure, подключенная к приложению, недоступна через конечные точки FTP или FTPs службы приложений. Используйте [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/).
- Служба хранилища Azure **не входит** в веб-приложение и оплачивается отдельно. Дополнительные сведения о [ценах на службу хранилища Azure](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> Конфигурации службы приложений, использующие хранилище BLOB-объектов Azure, будут доступны только для чтения в 2020 февраля. [Дополнительные сведения](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Настройка приложения с помощью службы хранилища Azure

После создания [учетной записи хранения Azure, общей папки и каталога](#prerequisites)вы можете настроить приложение в службе хранилища Azure.

Чтобы подключить учетную запись хранения к каталогу в приложении службы приложений, используйте [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) команду. Тип хранилища может быть AzureBlob или AzureFiles. В этом примере используется AzureFiles. Параметр пути подключения соответствует папке, которую вы хотите подключить из службы хранилища Azure. Если задать для него значение "/", будет подключена вся служба хранилища Azure.


> [!CAUTION]
> Каталог, указанный в качестве пути подключения в веб-приложении, должен быть пустым. Любое содержимое, хранящееся в этом каталоге, будет удалено при добавлении внешнего подключения. Если в существующем приложении выполняется перенос файлов, перед началом работы создайте резервную копию приложения и его содержимого.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Для всех остальных каталогов, которые вы хотите связать с учетной записью хранения, следует сделать то же самое.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Проверка ссылки на службу хранилища Azure на веб-приложение

Проверить, связан ли контейнер хранилища с веб-приложением, можно, выполнив следующую команду.

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Использование службы хранилища Azure в Docker Compose

Службу хранилища Azure можно подключить с помощью многоконтейнерных приложений, используя пользовательский идентификатор. Чтобы просмотреть имя настраиваемого идентификатора, выполните команду [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list) .

В файле *DOCKER-Compose. yml* сопоставьте `volumes` параметр с `custom-id` . Пример:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка веб-приложений в службе приложений Azure](../configure-common.md).

