---
title: Как создать общую папку в Azure | Документация Майкрософт
description: Как создать общую папку Azure в службе файлов Azure с помощью портала Azure, PowerShell и Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3b30df7410f56c203eb600089cce130a8de23d1b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514606"
---
# <a name="create-a-file-share-in-azure-files"></a>Создание общей папки в службе файлов Azure
Создавать общие папки Azure можно с помощью  [портала Azure](https://portal.azure.com/), командлетов PowerShell службы хранилища Azure, клиентских библиотек службы хранилища Azure и REST API службы хранилища Azure. Из этого руководства вы узнаете:
* как создать общую папку Azure с помощью портала Azure;
* [как создать общую папку Azure с помощью PowerShell](#create-file-share-through-powershell);
* [Создание файлового ресурса Azure с помощью CLI](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Технические условия
Чтобы создать общую папку в Azure, вы можете использовать существующую учетную запись хранения Azure или [создать новую](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Чтобы создать общую папку в Azure с помощью PowerShell, вам потребуется ключ учетной записи и имя учетной записи хранения. Если вы планируете использовать PowerShell или CLI, вам потребуется ключ учетной записи хранения.

> [!NOTE]
> Если вы хотите создать общие файловые ресурсы размером более 5 Тиб, см. статью [Включение больших файловых ресурсов](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Создание файлового ресурса с помощью портала Azure
1. **Перейдите в колонку учетной записи хранения на портале Azure**:    
    ![Колонка учетной записи хранения](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Нажмите кнопку "Общая папка"** :    
    ![Кнопка "Общая папка"](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Укажите имя и квоту**:    
    ![Указание имени и необходимой квоты для новой общей папки](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Просмотрите новую общую папку**: ![просмотр новой общей папки](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Отправьте файл**: ![отправка файла](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Перейдите в общую папку для управления каталогами и файлами**: ![обзор общей папки](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Создание общей папки с помощью PowerShell
Для подготовки к использованию PowerShell загрузите и установите командлеты Azure PowerShell. См.  [инструкции по установке и настройке Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

> [!Note]  
> Рекомендуется загрузить и установить или обновить версию модуля Azure PowerShell.

1. **Создайте новую учетную запись хранения:** Учетная запись хранения — это общий пул хранилища, в котором можно развертывать файловые ресурсы Azure, а также другие ресурсы хранилища, такие как большие двоичные объекты или очереди.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

2. **Создайте новую общую папку**:    
    
    ```powershell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> Имя общей папки должно состоять из символов в нижнем регистре. См. дополнительные сведения о  [присвоении имен общим ресурсам, каталогам, файлам и метаданным и создание ссылок на них](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Создание общей папки с помощью интерфейса командной строки (CLI)
1. **Чтобы подготовиться к использованию интерфейса командной строки (CLI), скачайте и установите Azure CLI.**  
    См. дополнительные сведения об  [установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и [использовании Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Создайте строку подключения для учетной записи хранения, где нужно создать общую папку.**  
    В примере ниже замените значения  ```<storage-account>```  и  ```<resource_group>```  именем учетной записи хранения и именем группы ресурса:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Создание файлового ресурса**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Дальнейшие действия
* [Mount an Azure File share and access the share in Windows](storage-how-to-use-files-windows.md) (Подключение файлового ресурса Azure и доступ к нему в Windows)
* [Использование хранилища файлов Azure в Linux](../storage-how-to-use-files-linux.md)
* [Mount Azure File share over SMB with macOS](storage-how-to-use-files-mac.md) (Использование хранилища файлов Azure с помощью протокола SMB в macOS)

Дополнительные сведения о службе "Файлы Azure" см. по следующим ссылкам.

* [ЧАСТО ЗАДАВАЕМЫЕ ВОПРОСЫ](../storage-files-faq.md)
* [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Устранение неполадок хранилища файлов Azure в Windows)      
* [Troubleshoot Azure File storage problems in Linux](storage-troubleshoot-linux-file-connection-problems.md) (Устранение неполадок хранилища файлов Azure в Linux)   
