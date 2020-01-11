---
title: Добавление пользовательского хранилища (контейнер Windows)
description: Узнайте, как присоединить настраиваемую сетевую папку в пользовательском контейнере Windows в службе приложений Azure. Совместное использование файлов между приложениями, удаленное управление статическим содержимым и локальный доступ и т. д.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: c5543470f790d00158297cb7c3f0c06c5fc05e14
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866985"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Настройка файлов Azure в контейнере Windows в службе приложений

> [!NOTE]
> Эта статья относится к пользовательским контейнерам Windows. Сведения о развертывании в службе приложений на платформе _Linux_см. в статье [обслуживание содержимого из службы хранилища Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

В этом руководство показано, как получить доступ к службе хранилища Azure в контейнерах Windows. Поддерживаются только общие папки службы [файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) и [файлы Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) . В этом пошаговом окне вы используете общие папки службы файлов Azure. Преимущества включают защищенное содержимое, его переносимость, доступ к нескольким приложениям и несколько методов для передачи.

## <a name="prerequisites"></a>Технические условия

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 или более поздней версии).
- [Существующее приложение контейнера Windows в службе приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Создание файлового ресурса Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Отправка файлов в общую папку Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Служба "файлы Azure" не является хранилищем по умолчанию и оплачивается отдельно, но не входит в состав веб-приложения. Не поддерживает использование конфигурации брандмауэра из-за ограничений инфраструктуры.
>

## <a name="limitations"></a>Ограничения

- Служба хранилища Azure в контейнерах Windows находится **на этапе предварительной версии** и **не поддерживается** в **рабочих сценариях**.
- Служба хранилища Azure в контейнерах Windows поддерживает подключение **контейнеров файлов Azure** (только для чтения и записи).
- В настоящее время служба хранилища Azure в контейнерах Windows **не поддерживается** для использования собственных сценариев работы с кодом в планах службы приложений Windows.
- Служба хранилища Azure в контейнерах Windows **не поддерживает** использование конфигурации **брандмауэра хранилища** из-за ограничений инфраструктуры.
- Служба хранилища Azure в контейнерах Windows позволяет указать **до пяти** точек подключения для каждого приложения.
- Плата за хранилище Azure взимается отдельно и **не включается** в веб-приложение. Дополнительные сведения о [ценах на службу хранилища Azure](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Связывание хранилища с веб-приложением (предварительная версия)

 Чтобы подключить файловый ресурс Azure к каталогу в приложении службы приложений, используйте команду [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) . Тип хранилища должен быть AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Это необходимо сделать для всех каталогов, которые необходимо связать с общим ресурсом службы файлов Azure.

## <a name="verify"></a>Проверка

Когда общая папка службы файлов Azure будет связана с веб-приложением, вы можете проверить это, выполнив следующую команду:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Дальнейшие действия

- [Перенос приложения ASP.NET в службу приложений Azure с помощью контейнера Windows (Предварительная версия)](app-service-web-tutorial-windows-containers-custom-fonts.md).
