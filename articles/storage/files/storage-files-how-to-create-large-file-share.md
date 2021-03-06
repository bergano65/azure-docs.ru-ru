---
title: Включение и создание больших файловых ресурсов — файлы Azure
description: В этой статье вы узнаете, как включить и создать большие файловые ресурсы.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422726"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Как включить и создать большие файловые ресурсы

Изначально стандартные общие файловые ресурсы можно масштабировать только до 5 тиб. Теперь при наличии больших файловых ресурсов они могут масштабироваться до 100 тиб. По умолчанию масштабируемые файловые ресурсы уровня "Премиум" масштабируются до 100 тиб. 

Чтобы масштабировать до 100 Тиб с помощью стандартных файловых ресурсов, необходимо включить учетную запись хранения для использования больших файловых ресурсов. Можно либо включить существующую учетную запись, либо создать новую учетную запись для использования больших файловых ресурсов.

## <a name="prerequisites"></a>предварительным требованиям

- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/) , прежде чем начинать работу.
- Если вы планируете использовать Azure CLI, убедитесь, что [установлена последняя версия](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Если вы планируете использовать Azure PowerShell, убедитесь, что [установлена последняя версия](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Ограничения

Учетные записи с поддержкой больших файловых ресурсов поддерживают LRS или ZRS. Сейчас учетные записи с поддержкой больших файловых ресурсов не поддерживают ГЗРС, GRS или RA-GRS. Включение больших файловых ресурсов в учетной записи является необратимым процессом. После включения учетная запись не может быть преобразована в ГЗРС, GRS или RA-GRS.

## <a name="create-a-new-storage-account"></a>Создание новой учетной записи хранения

### <a name="portal"></a>Портал

Войдите на [портале Azure](https://portal.azure.com).

1. На портале Azure щелкните **Все службы**. В списке ресурсов введите **Учетные записи хранения**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Учетные записи хранения**.
1. В появившемся окне **Учетные записи хранения** выберите **добавить**.
1. Выберите подписку, в которой будет создана учетная запись хранения.
1. В поле **Группа ресурсов** выберите **Создать**. Введите имя для новой группы ресурсов, как показано на следующем рисунке.

    ![Снимок экрана, на котором показано, как создавать группу ресурсов на портале](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Далее введите имя своей учетной записи хранения. Выбранное вами имя должно быть уникальным в Azure. Также имя должно содержать от 3 до 24 символов и может состоять только из цифр и строчных букв.
1. Выберите расположение учетной записи хранения, убедитесь, что это [один из регионов, поддерживаемых для LFS](storage-files-planning.md#regional-availability).
1. Настройте репликацию на **локально избыточное хранилище** или **хранилище, избыточное**в виде зоны.
1. Оставьте в следующих полях значения по умолчанию.

   |Поле  |Значение  |
   |---------|---------|
   |Модель развертывания     |Диспетчер ресурсов         |
   |Производительность     |Standard         |
   |Тип учетной записи     |StorageV2 (учетная запись общего назначения версии 2)         |
   |Уровень доступа     |Горячий         |

1. Выберите **Дополнительно** и выберите **включено** для **больших файловых ресурсов**.
1. Выберите **Просмотр и создание**, чтобы просмотреть настройки учетной записи хранения и создать учетную запись.

    ![Ларже-филе-ШАРЕС-Адванцед-енабле. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Нажмите кнопку **Создать**.


### <a name="cli"></a>Интерфейс командной строки

Сначала убедитесь, что [установлена последняя версия](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), чтобы можно было включить эту функцию.

Чтобы создать учетную запись хранения с включенными большими файловыми ресурсами, замените `<yourStorageAccountName>`, `<yourResourceGroup>`и `<yourDesiredRegion>` значениями, а затем используйте следующую команду:

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Сначала убедитесь, что [установлена последняя версия](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0), чтобы можно было включить эту функцию.

Чтобы создать учетную запись хранения с включенными большими файловыми ресурсами, замените `<yourStorageAccountName>`, `<yourResourceGroup>`и `<yourDesiredRegion>` значениями, а затем используйте следующую команду:

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Включить для существующей учетной записи

### <a name="portal"></a>Портал

Кроме того, можно включить большие файловые ресурсы в существующих учетных записях. В этом случае учетная запись больше не сможет преобразовать в ГЗРС, GRS или RA-GRS. Этот вариант необратим для этой учетной записи.

1. Откройте [портал Azure](https://portal.azure.com) и перейдите к учетной записи хранения, в которой вы хотите включить большие файловые ресурсы.
1. Откройте учетную запись хранения и выберите **Конфигурация**.
1. Выберите **включено** в **больших файловых ресурсах**, а затем нажмите кнопку Сохранить.
1. Выберите **Обзор** и щелкните **Обновить**.

![енабле-Ларже-филе-ШАРЕС-он-ексистинг. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Теперь вы включили большие файловые ресурсы в учетную запись хранения.

Если появляется следующая ошибка: "большие файловые ресурсы еще недоступны для этой учетной записи". Вы можете подождать некоторое время, так как ваш регион, вероятно, находится в середине завершения развертывания, или, если у вас есть срочные потребности, обращайтесь в службу поддержки.

### <a name="cli"></a>Интерфейс командной строки

Вы можете включить большие файловые ресурсы в существующих учетных записях. В этом случае учетная запись больше не сможет преобразовать в ГЗРС, GRS или RA-GRS. Этот вариант необратим для этой учетной записи.

Замените `<yourStorageAccountName>` и `<yourResourceGroup>` в следующей команде, а затем используйте ее для включения больших файловых ресурсов в существующей учетной записи:

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Вы можете включить большие файловые ресурсы в существующих учетных записях. В этом случае учетная запись больше не сможет преобразовать в ГЗРС, GRS или RA-GRS. Этот вариант необратим для этой учетной записи.

Замените `<yourStorageAccountName>` и `<yourResourceGroup>` в следующей команде, а затем используйте ее для включения больших файловых ресурсов в существующей учетной записи:

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Создание большой общей папки

### <a name="portal"></a>Портал

Создание больших файловых ресурсов практически идентично созданию стандартного файлового ресурса. Основное отличие заключается в том, что можно установить квоту до 100 тиб.

1. В учетной записи хранения выберите **файловые ресурсы**.
1. Выберите **+ Общая папка**.
1. Введите имя общей папки и (необязательно) нужный размер квоты до 100 Тиб, а затем нажмите кнопку **создать**. 

![Ларже-филе-ШАРЕС-креате-шаре. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>Интерфейс командной строки

После включения больших файловых ресурсов в учетной записи хранения можно создать общие файловые ресурсы в этой учетной записи с более высокими квотами. Замените `<yourStorageAccountName>`, `<yourStorageAccountKey>`и `<yourFileShareName>` в следующей команде на значения, затем его можно использовать для создания большого файлового ресурса:

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

После включения больших файловых ресурсов в учетной записи хранения можно создать общие файловые ресурсы в этой учетной записи с более высокими квотами. Замените `<YourStorageAccountName>`, `<YourStorageAccountKey>`и `<YourStorageAccountFileShareName>` в следующей команде на значения, затем его можно использовать для создания большого файлового ресурса:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Развертывание существующих файловых ресурсов

### <a name="portal"></a>Портал

После включения больших файловых ресурсов в учетной записи хранения можно расширить существующие общие папки до более высокой квоты.

1. В учетной записи хранения выберите **файловые ресурсы**.
1. Щелкните правой кнопкой мыши файловый ресурс и выберите **Квота**.
1. Введите новый требуемый размер, а затем нажмите кнопку **ОК**.

![упдате-Ларже-филе-шаре-куота. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>Интерфейс командной строки

После включения больших файловых ресурсов в учетной записи хранения можно развернуть существующие файловые ресурсы в этой учетной записи на более высокие квоты. Замените `<yourStorageAccountName>`, `<yourStorageAccountKey>`и `<yourFileShareName>` в следующей команде на значения, а затем используйте его, чтобы задать для квоты максимальный размер:

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

После включения больших файловых ресурсов в учетной записи хранения можно развернуть существующие файловые ресурсы в этой учетной записи на более высокие квоты. Замените `<YourStorageAccountName>`, `<YourStorageAccountKey>`и `<YourStorageAccountFileShareName>` в следующей команде на значения, а затем используйте его, чтобы задать для квоты максимальный размер:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Дополнительная информация

* [Mount an Azure File share and access the share in Windows](storage-how-to-use-files-windows.md) (Подключение файлового ресурса Azure и доступ к нему в Windows)
* [Использование хранилища файлов Azure в Linux](../storage-how-to-use-files-linux.md)
* [Mount Azure File share over SMB with macOS](storage-how-to-use-files-mac.md) (Использование хранилища файлов Azure с помощью протокола SMB в macOS)