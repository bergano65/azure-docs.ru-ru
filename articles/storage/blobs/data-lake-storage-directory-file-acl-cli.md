---
title: Использование Azure CLI для файлов и ACL в Azure Data Lake Storage 2-го поколения
description: Использование Azure CLI для управления каталогами, файлами и списком управления доступом (ACL) каталога в учетных записях хранения с иерархическим пространством имен.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 8fdcad18ccec2748761cf35f2cd0b8efe9749958
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466142"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Использование Azure CLI для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения

В этой статье показано, как использовать [интерфейс командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) для создания каталогов, файлов и разрешений в учетных записях хранения с иерархическим пространством имен, а также управления ими. 

[Сопоставление 1-го и 2-го поколения](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [Отправка отзывов](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * В учетной записи хранения включено иерархическое пространство имен. Выполните [эти](data-lake-storage-quickstart-create-account.md) инструкции, чтобы создать учетную запись.
> * Azure CLI версии`2.6.0` или выше.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Убедитесь, что у вас установлена правильная версия Azure CLI

1. Откройте [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) или, если вы [установили](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI локально, командное консольное приложение (например, Windows PowerShell).

2. Убедитесь, что установлена версия Azure CLI`2.6.0` или выше, выполнив следующую команду.

   ```azurecli
    az --version
   ```
   Если ваша версия Azure CLI ниже чем `2.6.0`, установите более позднюю версию. Подробнее см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-account"></a>Подключение к учетной записи

1. Если вы используете Azure CLI локально, выполните команду для входа.

   ```azurecli
   az login
   ```

   Если в CLI можно запустить браузер по умолчанию, откроется браузер со страницей входа.

   В противном случае самостоятельно откройте в браузере страницу [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и введите код авторизации, отображаемый в терминале. Затем выполните вход в браузере с помощью учетных данных.

   Дополнительные сведения о различных методах аутентификации см. на странице [Авторизация доступа к данным BLOB-объектов или очередей с помощью Azure CLI](../common/authorize-data-operations-cli.md).

2. Если удостоверение связано с несколькими подписками, установите активную подписку в качестве подписки учетной записи хранения, в которой будет размещен статический веб-сайт.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Замените значение заполнителя `<subscription-id>` идентификатором своей подписки.

> [!NOTE]
> В примере этой статьи показана авторизация Azure Active Directory. Дополнительные сведения о методах аутентификации см. на странице [Авторизация доступа к данным BLOB-объектов или очередей с помощью Azure CLI](../common/authorize-data-operations-cli.md).

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система выступает в качестве контейнера для файлов. Вы можете создать ее с помощью команды `az storage fs create`. 

В этом примере создается файловая система с именем `my-file-system`.

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>Отображение свойств файловой системы

Свойства файловой системы можно вывести на консоль с помощью команды `az storage fs show`.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>Вывод списка содержимого файловой системы

Выведите содержимое каталога с помощью команды `az storage fs file list`.

В этом примере выводится содержимое файловой системы с именем `my-file-system`.

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>Удаление файловой системы

Удалите файловую систему, выполнив команду `az storage fs delete`.

В этом примере удаляется файловая система с именем `my-file-system`. 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог с помощью команды `az storage fs directory create`. 

В этом примере каталог `my-directory` добавляется в файловую систему `my-file-system`, расположенную в учетной записи `mystorageaccount`.

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Отображение свойств каталога

Свойства каталога можно вывести на консоль с помощью команды `az storage fs directory show`.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог с помощью команды `az storage fs directory move`.

В этом примере каталог `my-directory` переименован на `my-new-directory` в той же файловой системе.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

В этом примере каталог перемещается в файловую систему `my-second-file-system`.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог с помощью команды `az storage fs directory delete`.

В этом примере удаляется каталог `my-directory`. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Проверка наличия каталога

Определите, существует ли определенный каталог в файловой системе, с помощью команды `az storage fs directory exists`.

В этом примере проверяется наличие каталога `my-directory` в файловой системе `my-file-system`. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Скачивание из каталога

Скачайте файл из каталога, используя команду `az storage fs file download`.

В этом примере из каталога `my-directory` скачивается файл `upload.txt`. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Выведите содержимое каталога с помощью команды `az storage fs file list`.

В этом примере выводится содержимое каталога `my-directory`, расположенного в файловой системе `my-file-system` учетной записи хранения `mystorageaccount`. 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Отправка файла в каталог

Отправьте файл в каталог с помощью команды `az storage fs directory upload`.

В этом примере в каталог `my-directory` отправляется файл `upload.txt`. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Отображение свойств файла

Свойства файла можно вывести на консоль с помощью команды `az storage fs file show`.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Переименование или перемещение файла

Переименуйте или переместите файл с помощью команды `az storage fs file move`.

В этом примере файл `my-file.txt` переименовывается на `my-file-renamed.txt`.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Удаление файла

Удалите файл, выполнив команду `az storage fs file delete`.

В этом примере удаляется файл с именем `my-file.txt`.

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>Управление разрешениями

Вы можете получать, задавать и обновлять разрешения на доступ к каталогам и файлам.

> [!NOTE]
> Если вы используете Azure Active Directory для авторизации команд, убедитесь, что субъекту безопасности назначена [роль владельца данных BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и последствиях их изменения см. на странице [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Получение списка ACL

Получите список ACL для **каталога** с помощью команды `az storage fs access show`.

В этом примере список ACL сначала извлекается для каталога, а затем выводится на консоль.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Получите права доступа к **файлу** с помощью команды `az storage fs access show`. 

В этом примере список ACL сначала извлекается для файла, а затем выводится на консоль.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

На следующем изображении показаны выходные данные после получения списка ACL для каталога.

![Получение выходных данных ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

В этом примере владелец имеет разрешения на чтение, запись и выполнение. Группа-владелец имеет разрешения только на чтение и выполнение. Дополнительные сведения о списках управления доступом в Azure Data Lake Storage 2-го поколения см. на [этой странице](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Настройка списка ACL

Используйте команду `az storage fs access set`, чтобы задать список ACL для **каталога**. 

В этом примере список ACL задается для каталога владельца, группы-владельца или других пользователей, а затем выводится на консоль.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

В этом примере список ACL *по умолчанию* задается в каталоге для владельца, группы-владельца или других пользователей, а затем выводится на консоль.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Используйте команду `az storage fs access set`, чтобы задать список ACL для **файла**. 

В этом примере список ACL задается для файла владельца, группы-владельца или других пользователей, а затем выводится на консоль.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

На следующем изображении показаны выходные данные после установки списка ACL для файла.

![Получение выходных данных ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

В этом примере владелец и группа-владелец имеют разрешения только на чтение и запись. У всех остальных пользователей есть разрешения на запись и выполнение. Дополнительные сведения о списках управления доступом в Azure Data Lake Storage 2-го поколения см. на [этой странице](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Обновление списка ACL

Другим способом установки этого разрешения является использование команды `az storage fs access set`. 

Обновите список ACL для каталога или файла, установив параметр `-permissions` в краткой форме ACL.

В этом примере список ACL обновляется для **каталога**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

В этом примере список ACL обновляется для **файла**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Вы также можете сделать обновление для владельца и группы каталога или файла, установив параметры `--owner` или `group` для пользовательского имени субъекта-пользователя или идентификатора сущности. 

В этом примере изменяется владелец каталога. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

В этом примере изменяется владелец файла. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>См. также раздел

* [Сопоставление 1-го и 2-го поколения](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Отправка отзывов](https://github.com/Azure/azure-cli-extensions/issues)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


