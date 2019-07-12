---
title: Настройка хранилища с помощью службы файлов Azure
description: Как настроить и подключение к файлам Azure в контейнере Windows в службе приложений.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789050"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Настройка службы файлов Azure в контейнере Windows в службе приложений

> [!NOTE]
> Эта статья относится к пользовательских контейнеров Windows. Чтобы развернуть службу приложений на _Linux_, см. в разделе [предоставления содержимого из службы хранилища Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

В этом руководстве показано, как получить доступ к хранилищу Azure в контейнерах Windows. Только [файловых ресурсах Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) и [файловым ресурсам уровня "премиум"](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) поддерживаются. В этом практическом руководстве используйте файловых ресурсах Azure. Преимущества включают защищенное содержимое, его переносимость, доступ к нескольким приложениям и несколько методов для передачи.

## <a name="prerequisites"></a>предварительные требования

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 или более поздней версии).
- [Существующее приложение контейнера Windows в службе приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Создать файловый ресурс Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Отправка файлов в общую папку Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Служба файлов Azure — это хранилище не по умолчанию и оплачиваются отдельно, не входит в состав веб-приложения. Он не поддерживает, с помощью конфигурации брандмауэра из-за ограничений инфраструктуры.
>

## <a name="link-storage-to-your-web-app-preview"></a>Связывание хранилища с веб-приложением (предварительная версия)

 Для подключения общей папки службы файлов Azure в каталог, в приложении службы приложений, использовании [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) команды. Тип хранилища должен быть переноса.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Это следует сделать для других каталогов, которые вы хотите связать со службой файлов Azure на общую папку.

## <a name="verify"></a>Проверка

После общей папки Azure связана с веб-приложения, это можно проверить, выполнив следующую команду:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Следующие шаги

- [Перенос приложения ASP.NET в службе приложений Azure с помощью контейнера Windows (Предварительная версия)](app-service-web-tutorial-windows-containers-custom-fonts.md).