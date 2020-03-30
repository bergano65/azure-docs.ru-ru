---
title: Добавление пользовательского хранилища (контейнер Windows)
description: Узнайте, как прикрепить пользовательскую долю сети в пользовательском контейнере Windows в службе приложений Azure. Делитесь файлами между приложениями, управляйте статическим содержимым удаленно и доступом локально и т.д.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120671"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Настройка файлов Azure в контейнере Windows в службе приложений

> [!NOTE]
> Эта статья относится к пользовательским контейнерам Windows. Чтобы развернуть в Службу приложений на _Linux,_ [см.](./containers/how-to-serve-content-from-azure-storage.md)
>

В этом руководстве показано, как получить доступ к хранению Azure в windows Containers. Поддерживаются только [акции файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) и [премиум-файлы.](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) Вы используете акции файлов Azure в этом способе. Преимущества включают защищенное содержимое, его переносимость, доступ к нескольким приложениям и несколько методов для передачи.

## <a name="prerequisites"></a>Предварительные требования

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 или более поздней версии).
- [Существующее приложение Windows Container в службе приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Создание совместного файла Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Загрузка файлов в общий обмен файлами Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files не по умолчанию хранения и выставлен счет отдельно, не включены в веб-приложение. Он не поддерживает использование конфигурации Firewall из-за ограничений инфраструктуры.
>

## <a name="limitations"></a>Ограничения

- Хранилище Azure в контейнерах Windows находится **в предварительном просмотре** и **не поддерживается** в **производственных сценариях.**
- Хранение Azure в контейнерах Windows поддерживает установку **контейнеров Azure Files** (читай/ запись) только.
- В настоящее время azure Storage в контейнерах Windows **не поддерживается** для использования собственных сценариев кода в планах Службы Windows.
- Хранилище Azure в контейнерах Windows **не поддерживает** использование конфигурации **хранилища брандмауэра** из-за ограничений инфраструктуры.
- Хранение Azure в контейнерах Windows позволяет указать **до пяти** точек крепления в приложении.
- Azure Storage, установленный на приложение, недоступен через конечные точки App Service FTP/FTPs. Используйте [исследователь хранения azure](https://azure.microsoft.com/features/storage-explorer/).
- Лазурный хранилище выставляется независимо и **не включается** в ваше веб-приложение. Узнайте больше о [ценах на хранение Azure.](https://azure.microsoft.com/pricing/details/storage)

## <a name="link-storage-to-your-web-app-preview"></a>Связывание хранилища с веб-приложением (предварительная версия)

 Чтобы установить раздел файлов Azure в каталог в приложении [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) Службы приложений, вы используете команду. Тип хранилища должен быть AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Это необходимо сделать для любых других каталогов, которые вы хотите соединить с разделом файлов Azure.

## <a name="verify"></a>Проверка

После того, как доля файлов Azure связана с веб-приложением, вы можете проверить это, запустив следующую команду:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Дальнейшие действия

- [Перенести ASP.NET приложение в службу приложений Azure с помощью контейнера Windows (Preview)](app-service-web-tutorial-windows-containers-custom-fonts.md).
