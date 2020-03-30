---
title: Включить и создать большие файлы - Azure Files
description: В этой статье вы узнаете, как включить и создать большие файлы.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c683e9847864de4e3409fb6dbd533497a5ae3cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061001"
---
# <a name="enable-and-create-large-file-shares"></a>Включить и создать большие файлы

При вменить большие файлы на свой счет хранения, ваши файлы могут масштабироваться до 100 TiB. Это масштабирование можно включить в существующие учетные записи хранилища для существующих файлов.

## <a name="prerequisites"></a>Предварительные требования

- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Если вы собираетесь использовать Azure CLI, [установите последнюю версию.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Если вы собираетесь использовать Azure PowerShell, [установите последнюю версию.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="restrictions"></a>Ограничения

На данный момент можно использовать локально-избыточное хранилище (LRS) или хранилище, избыточное зоной (ЗРС) только на больших учетных записях, включенных в раздел файла. Нельзя использовать геозонно-избыточное хранилище (ГЗРС), геоизбыточное хранилище (GRS) или гео-образное хранилище (RA-GRS).
Включение больших файлов на счет является необратимым процессом. После включения этого аккаунта не будет преобразована в G'S, GRS или RA-GRS.

## <a name="create-a-new-storage-account"></a>Создание новой учетной записи хранения

### <a name="portal"></a>Портал

1. Войдите на [портал Azure](https://portal.azure.com).
1. На портале Azure выберите **все сервисы.** 
1. В списке ресурсов введите **учетные записи хранения.** По мере ввода символов список отфильтруется соответствующим образом. Выберите **Учетные записи хранения**.
1. На появляются окна **учетных записей хранения,** **выберите Добавить**.
1. Выберите подписку, которую вы будете использовать для создания учетной записи хранилища.
1. В поле **Группа ресурсов** выберите **Создать**. Введите имя новой группы ресурсов.

    ![Снимок экрана, на котором показано, как создавать группу ресурсов на портале](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Далее введите имя своей учетной записи хранения. Это имя должно быть уникальным в пределах Azure. Название также должно быть от 3 до 24 символов в длину, и он может иметь только номера и буквы нижнего регистра.
1. Выберите место для учетной записи хранилища и убедитесь, что это [одна из поддерживаемых репликаций для больших файлов.](storage-files-planning.md#regional-availability)
1. Установите репликацию либо локально **избыточное хранилище,** либо **хранилище, избыточное зоной.**
1. Оставьте эти поля в своих значениях по умолчанию:

   |Поле  |Значение  |
   |---------|---------|
   |Модель развертывания     |Resource Manager         |
   |Производительность     |Standard         |
   |Тип учетной записи     |StorageV2 (учетная запись общего назначения версии 2)         |
   |Уровень доступа     |Горячий         |

1. Выберите **Расширенный**, а затем выберите кнопку **опции Enabled** справа от **больших акций файла.**
1. Выберите **Просмотр и создание**, чтобы просмотреть настройки учетной записи хранения и создать учетную запись.

    ![Скриншот с кнопкой опции "включена" на новой учетной записи хранилища на портале Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Выберите **Создать**.

### <a name="cli"></a>CLI

Во-первых, [установите последнюю версию Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) чтобы можно было включить большие файлы.

Для создания учетной записи хранилища с включенными крупными файлами используйте следующую команду. `<yourStorageAccountName>`Заменить `<yourResourceGroup>`, `<yourDesiredRegion>` и с вашей информацией.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Во-первых, [установите последнюю версию PowerShell,](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) чтобы вы могли включить большие файлы.

Для создания учетной записи хранилища с включенными крупными файлами используйте следующую команду. `<yourStorageAccountName>`Заменить `<yourResourceGroup>`, `<yourDesiredRegion>` и с вашей информацией.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Включить большие доли файлов на существующей учетной записи

Вы также можете включить большие файлы акций на существующих учетных записях. Если вы включите большие файлы, вы не сможете конвертировать в G'S, GRS или RA-GRS. Включение больших файлов необратимо на этом счете хранения.

### <a name="portal"></a>Портал

1. Откройте [портал Azure](https://portal.azure.com)и перейдите на учетную запись хранилища, где требуется включить большие файлы.
1. Откройте учетную запись хранилища и выберите **конфигурацию.**
1. Выберите **Включенные** на **больших долях файлов,** а затем выберите **Сохранить**.
1. Выберите **Обзор** и выберите **Обновление**.

![Выбор кнопки опции «Включено» на существующей учетной записи хранения на портале Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Теперь вы включили большие файлы на свой счет хранения. Далее необходимо обновить существующую квоту акций, чтобы воспользоваться преимуществами увеличения емкости и масштаба.

Если вы получили сообщение об ошибке "Большие файлы пока недоступны для учетной записи", ваш регион может быть в середине завершения развертывания. Обратитесь в службу поддержки, если у вас есть срочная потребность в больших файловых акциях.

### <a name="cli"></a>CLI

Для включения больших файлов в существующую учетную запись используйте следующую команду. `<yourStorageAccountName>` Замените `<yourResourceGroup>` и с вашей информацией.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Для включения больших файлов в существующую учетную запись используйте следующую команду. `<yourStorageAccountName>` Замените `<yourResourceGroup>` и с вашей информацией.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Создание большого общего файла

После включения больших файлов в учетную запись хранения можно создать файлы на этой учетной записи с более высокими квотами. 

### <a name="portal"></a>Портал

Создание большой доли файлов почти идентично созданию стандартной доли файлов. Основное отличие заключается в том, что можно установить квоту до 100 TiB.

1. На вашем счете хранения выберите **акции Файла.**
1. Выберите **+Файловый ресурс**.
1. Введите имя для своей доли файла. Вы также можете установить размер квоты, который вы хотите, до 100 TiB. Затем выберите **Создать**. 

![UI портала Azure, показывающий ящики с именем и квотами](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Чтобы создать большую общую часть файлов, используйте следующую команду. `<yourStorageAccountName>`Заменить `<yourStorageAccountKey>`, `<yourFileShareName>` и с вашей информацией.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Чтобы создать большую общую часть файлов, используйте следующую команду. `<YourStorageAccountName>`Заменить `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` и с вашей информацией.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Расширение существующих файлов

После включения больших файлов в учетную запись хранения можно также расширить существующие файлы на этом счете до более высокой квоты. 

### <a name="portal"></a>Портал

1. На вашем счете хранения выберите **акции Файла.**
1. Нажмите правой кнопкой мыши на общую часть файла, а затем выберите **Квота**.
1. Введите новый размер, который вы хотите, а затем выберите **OK.**

![UI портала Azure с квотой существующих файловых акций](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Чтобы установить квоту до максимального размера, используйте следующую команду. `<yourStorageAccountName>`Заменить `<yourStorageAccountKey>`, `<yourFileShareName>` и с вашей информацией.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Чтобы установить квоту до максимального размера, используйте следующую команду. `<YourStorageAccountName>`Заменить `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` и с вашей информацией.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение и монтаж файла в Windows](storage-how-to-use-files-windows.md)
* [Подключение и монтаж файла доля на Linux](storage-how-to-use-files-linux.md)
* [Подключение и монтаж файла доля на macOS](storage-how-to-use-files-mac.md)
