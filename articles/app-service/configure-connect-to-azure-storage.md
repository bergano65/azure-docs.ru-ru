---
title: Настройка хранилища с помощью службы файлов Azure
description: Настройка и подключение к файлам Azure в контейнере Windows в службе приложений.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 2c12bf45c033fea185d976f1e9d644183407b5ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297223"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Настройка файлов Azure в контейнере Windows в службе приложений

> [!NOTE]
> Эта статья относится к пользовательским контейнерам Windows. Сведения о развертывании в службе приложений на платформе _Linux_см. в статье [обслуживание содержимого из службы хранилища Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

В этом руководство показано, как получить доступ к службе хранилища Azure в контейнерах Windows. Поддерживаются только общие папки службы [файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) и [файлы Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) . В этом пошаговом окне вы используете общие папки службы файлов Azure. Преимущества включают защищенное содержимое, его переносимость, доступ к нескольким приложениям и несколько методов для передачи.

## <a name="prerequisites"></a>Предварительные требования

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 или более поздней версии).
- [Существующее приложение контейнера Windows в службе приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Создание файлового ресурса Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Отправка файлов в общую папку Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Служба "файлы Azure" не является хранилищем по умолчанию и оплачивается отдельно, но не входит в состав веб-приложения. Не поддерживает использование конфигурации брандмауэра из-за ограничений инфраструктуры.
>

## <a name="link-storage-to-your-web-app-preview"></a>Связывание хранилища с веб-приложением (предварительная версия)

 Чтобы подключить файловый ресурс Azure к каталогу в приложении службы приложений, используйте [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) команду. Тип хранилища должен быть AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Это необходимо сделать для всех каталогов, которые необходимо связать с общим ресурсом службы файлов Azure.

## <a name="verify"></a>Проверка

Когда общая папка службы файлов Azure будет связана с веб-приложением, вы можете проверить это, выполнив следующую команду:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Следующие шаги

- [Перенос приложения ASP.NET в службу приложений Azure с помощью контейнера Windows (Предварительная версия)](app-service-web-tutorial-windows-containers-custom-fonts.md).
