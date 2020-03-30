---
title: Используйте Azure CLI для файлов & ACL в Azure Data Lake Storage 2 (предварительный просмотр)
description: Используйте Azure CLI для управления каталогами и списками управления доступом файлов и каталогов (ACL) в учетных записях хранения, которые имеют иерархическое пространство имен.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486140"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Используйте Azure CLI для управления каталогами, файлами и ACL в Azure Data Lake Storage 2 (предварительный просмотр)

В этой статье показано, как использовать [интерфейс командования и линии Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) для создания и управления каталогами, файлами и разрешениями в учетных записях хранения, которые имеют иерархическое пространство имен. 

> [!IMPORTANT]
> Расширение, `storage-preview` которое фигурирует в этой статье, в настоящее время находится в открытом предварительном просмотре.

[Пример](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 к отображению](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | Gen2[Дайте обратную связь](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранилища с включенным иерархическим пространством имен (HNS). Следуйте [этим](data-lake-storage-quickstart-create-account.md) инструкциям, чтобы создать один.
> * Версия `2.0.67` Azure CLI или выше.

## <a name="install-the-storage-cli-extension"></a>Установка расширения хранилища CLI

1. Откройте [облачную оболочку Azure,](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)или если вы [установили](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI локально, откройте приложение командной консоли, такое как Windows PowerShell.

2. Убедитесь, что установленная версия Azure `2.0.67` CLI или выше, используя следующую команду.

   ```azurecli
    az --version
   ```
   Если версия Azure CLI ниже, чем, `2.0.67`установите более позднюю версию. Смотрите [Установить Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Установите расширение `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Подключение к учетной записи

1. Если вы используете Azure CLI локально, запустите команду входа.

   ```azurecli
   az login
   ```

   Если в CLI можно запустить браузер по умолчанию, откроется браузер со страницей входа.

   В противном случае [https://aka.ms/devicelogin](https://aka.ms/devicelogin) откройте страницу браузера и введите код авторизации, отображаемый в терминале. Затем вопийте учетные данные учетной записи в браузере.

   Дополнительные сведения о различных методах проверки подлинности см. в статье Вход с помощью Azure CLI.

2. Если ваша личность связана с более чем одной подпиской, то установите активную подписку на подписку на учетную запись хранения, в размещении которого будет размещен ваш статический веб-сайт.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Замените `<subscription-id>` значение заполнителя идентификатором вашей подписки.

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система действует как контейнер для файлов. Вы можете создать его `az storage container create` с помощью команды. 

Этот пример создает файловую систему под названием `my-file-system`.

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на `az storage blob directory create` каталог с помощью команды. 

Этот пример добавляет каталог `my-directory` с именем `my-file-system` в файловую систему под названием, которая находится в учетной записи с именем. `mystorageaccount`

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Показать свойства каталога

Свойства каталога можно распечатать с помощью `az storage blob show` команды.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуй или переместите каталог с помощью `az storage blob directory move` команды.

Этот пример переименовывает каталог `my-directory` от `my-new-directory`имени к имени .

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог с `az storage blob directory delete` помощью команды.

Этот пример удаляет каталог `my-directory`с именем . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Проверьте, существует ли каталог

Определите, существует ли определенный каталог в `az storage blob directory exist` файловой системе с помощью команды.

Этот пример показывает, существует `my-directory` ли в `my-file-system` файловой системе имя каталога. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Скачать из каталога

Загрузите файл из каталога `az storage blob directory download` с помощью команды.

Этот пример загружает файл, `upload.txt` `my-directory`названный из каталога под названием . 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Этот пример загружает весь каталог.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Перечислите содержимое каталога с `az storage blob directory list` помощью команды.

В этом примере перечисляется `my-directory` содержимое каталога `my-file-system` с именем, `mystorageaccount`расположенного в файловой системе аккаунта хранения под названием. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Загрузить файл в каталог

Загрузите файл в каталог `az storage blob directory upload` с помощью команды.

Этот пример загружает файл с именем `upload.txt` в каталог под названием `my-directory`. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Этот пример загружает весь каталог.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Показать свойства файла

Свойства файла можно распечатать с помощью `az storage blob show` команды.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Переименование или перемещение файла

Переименуй или переместите файл с помощью `az storage blob move` команды.

Этот пример переименовывает `my-file.txt` файл из `my-file-renamed.txt`имени в имя .

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Удаление файла

Удалите файл `az storage blob delete` с помощью команды.

Этот пример удаляет файл с именем`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Управление разрешениями

Вы можете получить, установить и обновить разрешения доступа каталогов и файлов.

> [!NOTE]
> Если вы используете Active Directory Azure (Azure AD) для авторизации команд, убедитесь, что директору безопасности была назначена [роль владельца данных Хранилища.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Чтобы узнать больше о том, как применяются разрешения [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ACL и о последствиях их изменения, см.

### <a name="get-directory-and-file-permissions"></a>Получение разрешений каталога и файлов

Получите ACL **каталога** с `az storage blob directory access show` помощью команды.

Этот пример получает ACL каталога, а затем печатает ACL на консоль.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Получите разрешение доступа **файла** с `az storage blob access show` помощью команды. 

Этот пример получает ACL файла, а затем печатает ACL на консоль.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Следующее изображение показывает выход после получения ACL каталога.

![Получить выход ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

В этом примере пользователь-владелик прочитал, написал и выполнил разрешения. Группа владельцев только читала и выполняла разрешения. Для получения дополнительной информации о списках управления доступом смотрите [элемент управления доступом в Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Установка каталога и разрешения файлов

Используйте `az storage blob directory access set` команду для установки ACL **каталога.** 

Этот пример устанавливает ACL в каталоге для пользователя-владелеца, владеющего группой или другими пользователями, а затем печатает ACL на консоль.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Этот пример устанавливает ACL *по умолчанию* в каталоге для пользователя-владелеца, владеющего группой или другими пользователями, а затем печатает ACL на консоль.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Используйте `az storage blob access set` команду для установки акля **файла.** 

Этот пример устанавливает ACL на файл для пользователя-владелец, владеющий группой или другими пользователями, а затем печатает ACL на консоль.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Следующее изображение показывает вывод после установки ACL файла.

![Получить выход ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

В этом примере пользователь-владелик и группа владельцев только читают и пишут разрешения. Все остальные пользователи имеют разрешение на запись и выполнение. Для получения дополнительной информации о списках управления доступом смотрите [элемент управления доступом в Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Обновление каталога и разрешений файлов

Другой способ установить это разрешение `az storage blob directory access update` `az storage blob access update` — использовать команду или команду. 

Обновление ACL каталога или файла, `-permissions` установив параметр для короткой формы ACL.

Этот пример обновляет ACL **каталога.**

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Этот пример обновляет ACL **файла.**

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Вы также можете обновить владелец пользователя и группы каталога или файла, установив `--owner` или `group` параметры для идентификатора сущности или имя пользователя (UPN) пользователя. 

Этот пример изменяет владельца каталога. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Этот пример изменяет владельца файла. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Управление метаданными, определяемыми пользователями

Метаданные, определяемые пользователем, можно `az storage blob directory metadata update` добавить в файл или каталог, используя команду с одной или нескольких парами имен.

Этот пример добавляет метаданные с определенным `my-directory` ими пользователя для каталога с именем каталога.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

В этом примере отображались все `my-directory`метаданные, определяемые пользователем, для каталога с именем .

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>См. также

* [Пример](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Отображение Gen1 до Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Отправка отзывов](https://github.com/Azure/azure-cli-extensions/issues)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [исходный код](https://github.com/Azure/azure-cli-extensions/tree/master/src).

