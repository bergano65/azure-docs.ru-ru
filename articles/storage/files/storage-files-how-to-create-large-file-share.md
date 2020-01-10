---
title: Включение и создание больших файловых ресурсов — файлы Azure
description: В этой статье вы узнаете, как включить и создать большие файловые ресурсы.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a9b545d71f21138c0374cf199ce10dc2dc246afb
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732150"
---
# <a name="enable-and-create-large-file-shares"></a>Включение и создание больших файловых ресурсов

Изначально стандартные общие файловые ресурсы можно масштабировать только до 5 тиб. Теперь с большими файловыми ресурсами можно масштабировать до 100 тиб. Вы можете включить это масштабирование в существующих учетных записях хранения для существующих файловых ресурсов. По умолчанию масштабируемые файловые ресурсы уровня "Премиум" масштабируются до 100 тиб.

## <a name="prerequisites"></a>Технические условия

- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
- Если вы планируете использовать Azure CLI, [установите последнюю версию](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Если вы планируете использовать Azure PowerShell, [установите последнюю версию](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Ограничения

Сейчас для учетных записей с поддержкой больших файловых ресурсов можно использовать только LRS или ZRS. Нельзя использовать ГЗРС, GRS или RA-GRS.
Включение больших файловых ресурсов в учетной записи является необратимым процессом. После включения учетной записи вы не сможете преобразовать ее в ГЗРС, GRS или RA-GRS.

## <a name="create-a-new-storage-account"></a>Создание новой учетной записи хранения

### <a name="portal"></a>Портал

1. Войдите на [портал Azure](https://portal.azure.com).
1. На портале Azure щелкните **Все службы**. 
1. В списке ресурсов введите **учетные записи хранения**. По мере ввода символов список отфильтруется соответствующим образом. Выберите **Учетные записи хранения**.
1. В появившемся окне **учетные записи хранения** выберите **Добавить**.
1. Выберите подписку, которая будет использоваться для создания учетной записи хранения.
1. В поле **Группа ресурсов** выберите **Создать**. Введите имя новой группы ресурсов.

    ![Снимок экрана, на котором показано, как создавать группу ресурсов на портале](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Далее введите имя своей учетной записи хранения. Это имя должно быть уникальным в пределах Azure. Имя также должно иметь длину от 3 до 24 символов и может содержать только цифры и строчные буквы.
1. Выберите расположение учетной записи хранения и убедитесь, что это [один из регионов, поддерживаемых большими файловыми ресурсами](storage-files-planning.md#regional-availability).
1. Настройте репликацию на **локально избыточное хранилище** или **хранилище, избыточное**в виде зоны.
1. Оставьте значения по умолчанию для этих полей:

   |Поле  |Значение  |
   |---------|---------|
   |Модель развертывания     |Диспетчер ресурсов         |
   |Ориентированное на производительность     |Стандартный         |
   |Тип учетной записи     |StorageV2 (учетная запись общего назначения версии 2)         |
   |Уровень доступа     |Горячий         |

1. Выберите **Дополнительно**, а затем нажмите кнопку **Enabled (включено** ) справа от **больших файловых ресурсов**.
1. Выберите **Просмотр и создание**, чтобы просмотреть настройки учетной записи хранения и создать учетную запись.

    ![Снимок экрана с переключателем "включено" в новой учетной записи хранения в портал Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Нажмите кнопку **Создать**.

### <a name="cli"></a>Интерфейс командной строки

Сначала [установите последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , чтобы можно было включить большие файловые ресурсы.

Чтобы создать учетную запись хранения с включенными большими файловыми ресурсами, используйте следующую команду. Замените `<yourStorageAccountName>`, `<yourResourceGroup>`и `<yourDesiredRegion>` информацией.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Сначала [установите последнюю версию PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) , чтобы можно было включить большие файловые ресурсы.

Чтобы создать учетную запись хранения с включенными большими файловыми ресурсами, используйте следующую команду. Замените `<yourStorageAccountName>`, `<yourResourceGroup>`и `<yourDesiredRegion>` информацией.

```PowerShell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Включение общих папок с большими файлами в существующей учетной записи

Вы также можете включить большие файловые ресурсы в существующих учетных записях. Если включить большие файловые ресурсы, вы не сможете преобразовать их в ГЗРС, GRS или RA-GRS. Включение больших файловых ресурсов необратимо в этой учетной записи хранения.

### <a name="portal"></a>Портал

1. Откройте [портал Azure](https://portal.azure.com)и перейдите к учетной записи хранения, в которой необходимо включить большие файловые ресурсы.
1. Откройте учетную запись хранения и выберите **Конфигурация**.
1. Выберите **включено** в **больших файловых ресурсах**, а затем нажмите кнопку **сохранить**.
1. Выберите **Обзор** и щелкните **Обновить**.

![Выбор параметра "включено" в существующей учетной записи хранения в портал Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Теперь вы включили большие файловые ресурсы в учетную запись хранения. Затем необходимо обновить квоту существующей общей папки, чтобы воспользоваться преимуществами увеличения емкости и масштабирования.

Если вы получаете сообщение об ошибке "большие файловые ресурсы еще недоступны для этой учетной записи", ваш регион может находиться в процессе развертывания. Если у вас есть срочные потребности в больших файловых ресурсах, обратитесь в службу поддержки.

### <a name="cli"></a>Интерфейс командной строки

Чтобы включить большие файловые ресурсы в существующей учетной записи, используйте следующую команду. Замените `<yourStorageAccountName>` и `<yourResourceGroup>` своими сведениями.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Чтобы включить большие файловые ресурсы в существующей учетной записи, используйте следующую команду. Замените `<yourStorageAccountName>` и `<yourResourceGroup>` своими сведениями.

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Создание большой общей папки

После включения больших файловых ресурсов в учетной записи хранения можно создать общие файловые ресурсы в этой учетной записи с более высокими квотами. 

### <a name="portal"></a>Портал

Создание больших файловых ресурсов практически идентично созданию стандартного файлового ресурса. Основное отличие заключается в том, что можно установить квоту до 100 тиб.

1. В учетной записи хранения выберите **файловые ресурсы**.
1. Выберите **+Файловый ресурс**.
1. Введите имя для общей папки. Вы также можете задать желаемый размер квоты до 100 тиб. Щелкните **Создать**. 

![портал Azure пользовательского интерфейса, отображающего поля имени и квоты](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>Интерфейс командной строки

Чтобы создать большой файловый ресурс, используйте следующую команду. Замените `<yourStorageAccountName>`, `<yourStorageAccountKey>`и `<yourFileShareName>` информацией.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Чтобы создать большой файловый ресурс, используйте следующую команду. Замените `<YourStorageAccountName>`, `<YourStorageAccountKey>`и `<YourStorageAccountFileShareName>` информацией.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Развертывание существующих файловых ресурсов

После включения больших файловых ресурсов в учетной записи хранения можно также развернуть существующие файловые ресурсы в этой учетной записи на более высокую квоту. 

### <a name="portal"></a>Портал

1. В учетной записи хранения выберите **файловые ресурсы**.
1. Щелкните правой кнопкой мыши файловый ресурс и выберите пункт **Квота**.
1. Введите новый необходимый размер и нажмите кнопку **ОК**.

![Пользовательский интерфейс портал Azure с квотой существующих файловых ресурсов](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>Интерфейс командной строки

Чтобы задать для квоты максимальный размер, используйте следующую команду. Замените `<yourStorageAccountName>`, `<yourStorageAccountKey>`и `<yourFileShareName>` информацией.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Чтобы задать для квоты максимальный размер, используйте следующую команду. Замените `<YourStorageAccountName>`, `<YourStorageAccountKey>`и `<YourStorageAccountFileShareName>` информацией.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Дальнейшие действия

* [Подключить и подключить общую папку в Windows](storage-how-to-use-files-windows.md)
* [Подключение и монтирование файлового ресурса в Linux](storage-how-to-use-files-linux.md)
* [Подключите и подключите общую папку на macOS](storage-how-to-use-files-mac.md)
