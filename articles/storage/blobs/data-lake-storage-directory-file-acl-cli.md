---
title: Использование Azure CLI для файлов & ACL в Azure Data Lake Storage 2-го поколения
description: Используйте Azure CLI для управления каталогами и списками управления доступом к файлам и каталогам (ACL) в учетных записях хранения, имеющих иерархическое пространство имен.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 79a87f02c8730e0b2110e7475de721f11beda568
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120612"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Использование Azure CLI для управления каталогами, файлами и списками ACL в Azure Data Lake Storage 2-го поколения

В этой статье показано, как использовать [интерфейс командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) для создания каталогов, файлов и разрешений в учетных записях хранения с иерархическим пространством имен и управления ими. 

Сопоставление Gen1 с [Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)  |  [Отправить отзыв](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Предварительные требования

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранения, в которой включено иерархическое пространство имен (HNS). Чтобы создать его [, выполните следующие](data-lake-storage-quickstart-create-account.md) инструкции.
> * Azure CLI версии `2.5.1` или выше.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Убедитесь, что установлена правильная версия Azure CLI

1. Откройте [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)или, если вы [установили](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI локально, Откройте консольное приложение командной строки, например Windows PowerShell.

2. Убедитесь, что установленная версия Azure CLI `2.5.1` или более поздняя, используя следующую команду.

   ```azurecli
    az --version
   ```
   Если ваша версия Azure CLI меньше `2.5.1` , установите более позднюю версию. Подробнее см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-account"></a>Подключение к учетной записи

1. Если вы используете Azure CLI локально, выполните команду Login.

   ```azurecli
   az login
   ```

   Если в CLI можно запустить браузер по умолчанию, откроется браузер со страницей входа.

   В противном случае откройте страницу браузера в [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и введите код авторизации, отображаемый в терминале. Затем выполните вход с использованием учетных данных своей учетной записи в браузере.

   Дополнительные сведения о различных методах проверки подлинности см. в разделе [авторизация доступа к данным BLOB-объектов или очередей с помощью Azure CLI](../common/authorize-data-operations-cli.md).

2. Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, в которой будет размещен статический веб-сайт.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Замените `<subscription-id>` значение ЗАПОЛНИТЕЛЯ идентификатором подписки.

> [!NOTE]
> В примере, приведенном в этой статье, показана авторизация Azure Active Directory (AD). Дополнительные сведения о методах авторизации см. в разделе [авторизация доступа к данным BLOB-объектов или очередей с помощью Azure CLI](../common/authorize-data-operations-cli.md).

## <a name="create-a-file-system"></a>Создание файловой системы

Файловая система выступает в качестве контейнера для файлов. Его можно создать с помощью `az storage fs create` команды. 

В этом примере создается файловая система с именем `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>Отображение свойств файловой системы

Свойства файловой системы можно вывести на консоль с помощью `az storage fs show` команды.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>Список содержимого файловой системы

Выведите список содержимого каталога с помощью `az storage fs file list` команды.

В этом примере выводится содержимое файловой системы с именем `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>Удаление файловой системы

Удалите файловую систему с помощью `az storage fs delete` команды.

В этом примере удаляется файловая система с именем `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Создание каталога

Создайте ссылку на каталог с помощью `az storage fs directory create` команды. 

В этом примере добавляется каталог с именем `my-directory` в файловую систему с именем `my-file-system` , который находится в учетной записи с именем `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Отображение свойств каталога

Свойства каталога можно вывести на консоль с помощью `az storage fs directory show` команды.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Переименование или перемещение каталога

Переименуйте или переместите каталог с помощью `az storage fs directory move` команды.

В этом примере каталог с именем переименовывается в имя `my-directory` `my-new-directory` в той же файловой системе.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

В этом примере каталог перемещается в файловую систему с именем `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Удаление каталога

Удалите каталог с помощью `az storage fs directory delete` команды.

В этом примере удаляется каталог с именем `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Проверка существования каталога

Определите, существует ли определенный каталог в файловой системе, с помощью `az storage fs directory exists` команды.

Этот пример показывает, существует ли каталог с именем `my-directory` в `my-file-system` файловой системе. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Скачать из каталога

Скачайте файл из каталога с помощью `az storage fs file download` команды.

В этом примере загружается файл с именем `upload.txt` из каталога с именем `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Вывод содержимого каталогов

Выведите список содержимого каталога с помощью `az storage fs file list` команды.

В этом примере выводится содержимое каталога с именем `my-directory` , расположенного в `my-file-system` файловой системе учетной записи хранения с именем `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Передача файла в каталог

Отправьте файл в каталог с помощью `az storage fs directory upload` команды.

В этом примере загружается файл с именем `upload.txt` в каталог с именем `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Отображение свойств файла

Свойства файла можно вывести на консоль с помощью `az storage fs file show` команды.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Переименование или перемещение файла

Переименуйте или переместите файл с помощью `az storage fs file move` команды.

В этом примере имя файла переименовывается в имя `my-file.txt` `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Удаление файла

Удалите файл с помощью `az storage fs file delete` команды.

В этом примере удаляется файл с именем`my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>Управление разрешениями

Можно получать, задавать и обновлять разрешения на доступ к каталогам и файлам.

> [!NOTE]
> Если вы используете Azure Active Directory (Azure AD) для авторизации команд, убедитесь, что участнику безопасности назначена [роль владельца данных BLOB-объекта хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Дополнительные сведения о применении разрешений ACL и их влиянии на их изменение см. в разделе [Контроль доступа в Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Получение списка ACL

Получите список ACL для **каталога** с помощью `az storage fs access show` команды.

Этот пример возвращает список ACL для каталога, а затем выводит список ACL на консоль.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Получите разрешения на доступ к **файлу** с помощью `az storage fs access show` команды. 

Этот пример возвращает список ACL для файла, а затем выводит список ACL на консоль.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

На следующем рисунке показаны выходные данные после получения списка управления доступом для каталога.

![Получить выходные данные ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

В этом примере пользователь-владелец имеет разрешения на чтение, запись и выполнение. Группа-владелец имеет только разрешения на чтение и выполнение. Дополнительные сведения о списках управления доступом см. [в разделе Контроль доступа в Azure Data Lake Storage 2-го поколения](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Настройка ACL

Используйте `az storage fs access set` команду, чтобы задать список управления доступом для **каталога**. 

Этот пример задает список ACL для каталога пользователя-владельца, группы-владельца или других пользователей, а затем выводит список ACL на консоль.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

В этом примере задается список ACL *по умолчанию* для каталога пользователя-владельца, группы-владельца или других пользователей, а затем на консоль выводится список ACL.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Используйте `az storage fs access set` команду, чтобы задать список управления доступом для **файла**. 

В этом примере список ACL задается для файла, владеющего пользователем, группой-владельцем или другими пользователями, а затем на консоль выводится список ACL.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

На следующем рисунке показаны выходные данные после установки списка управления доступом для файла.

![Получить выходные данные ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

В этом примере пользователь-владелец и группа-владелец имеют только разрешения на чтение и запись. Все остальные пользователи имеют разрешения на запись и выполнение. Дополнительные сведения о списках управления доступом см. [в разделе Контроль доступа в Azure Data Lake Storage 2-го поколения](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Обновление списка управления доступом

Другим способом задать это разрешение является использование `az storage fs access set` команды. 

Обновите список управления доступом для каталога или файла, задав `-permissions` для параметра краткую форму ACL.

В этом примере обновляется список управления доступом для **каталога**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

В этом примере обновляется список управления доступом для **файла**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Вы также можете обновить пользователя-владельца и группу каталога или файла, задав `--owner` `group` для параметров или идентификатор сущности или имя участника-пользователя (UPN) пользователя. 

В этом примере изменяется владелец каталога. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

В этом примере изменяется владелец файла. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>См. также раздел

* [Сопоставление Gen1 с Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Оставить отзыв](https://github.com/Azure/azure-cli-extensions/issues)
* [Известные проблемы](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


