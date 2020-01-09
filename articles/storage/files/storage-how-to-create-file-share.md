---
title: создать файловый ресурс Azure;
titleSuffix: Azure Files
description: Как создать файловый ресурс Azure с помощью портал Azure, PowerShell или Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452878"
---
# <a name="create-a-file-share"></a>Создание общей папки
Вы можете создать общую папку в службе файлов Azure с помощью [портал Azure](https://portal.azure.com/), командлетов PowerShell службы хранилища Azure, клиентских библиотек службы хранилища azure или REST API хранилища Azure. В этой статье вы узнаете, как выполнять следующие задачи.
- Создайте файловый ресурс Azure с помощью портал Azure
- Создание файлового ресурса Azure с помощью PowerShell
- Создайте файловый ресурс Azure с помощью Azure CLI

## <a name="prerequisites"></a>Технические условия
Чтобы создать файловый ресурс Azure, можно использовать учетную запись хранения, которая уже существует, или [создать новую учетную запись хранения Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Чтобы создать файловый ресурс Azure с помощью PowerShell, вам потребуется ключ учетной записи и имя вашей учетной записи хранения. Если вы планируете использовать PowerShell или CLI, вам потребуется ключ учетной записи хранения.

> [!NOTE]
> Если вы хотите создать общие файловые ресурсы размером более 5 Тиб, см. раздел [Включение больших файловых ресурсов](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Создание файлового ресурса с помощью портала Azure

1. Перейдите в область **учетной записи хранения** на портал Azure.
    панель ![учетной записи хранения](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Нажмите кнопку **+ Общая папка** .
    ![кнопку Добавить общую папку](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Введите сведения об **имени** и **квоте**.
    ![имя и квоту для нового файлового ресурса](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Просмотрите новый файловый ресурс.
    ![новый файловый ресурс в пользовательском интерфейсе](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Отправка файла.
    ![кнопку "Отправить" на панели навигации](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Найдите файловый ресурс и управляйте своими каталогами и файлами.
    ![представление общих папок](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Создание общей папки с помощью PowerShell

Скачайте и установите командлеты Azure PowerShell. См.  [инструкции по установке и настройке Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

> [!Note]  
> Рекомендуется загрузить и установить последнюю версию модуля Azure PowerShell или обновить до нее.

1. Создайте учетную запись хранения.
    Учетная запись хранения — это общий пул хранилища, в котором можно развернуть файловые ресурсы Azure и ресурсы хранилища, такие как большие двоичные объекты или очереди.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Создайте новый файловый ресурс.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> Имя общей папки должно состоять из символов в нижнем регистре. Полные сведения об именовании файловых ресурсов и файлов см. в разделе [именование и ссылки на общие папки, каталоги, файлы и метаданные](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-a-file-share-through-the-cli"></a>Создание общей папки с помощью интерфейса командной строки

1. Скачайте и установите Azure CLI.
    См. статью [установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и [начало работы с Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

1. Создайте строку подключения к учетной записи хранения, в которой нужно создать общую папку.
    В примере ниже замените значения  ```<storage-account>```  и  ```<resource_group>```  именем учетной записи хранения и именем группы ресурса:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Создайте общую папку.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Подключить и подключить общую папку в Windows](storage-how-to-use-files-windows.md)
* [Подключение и монтирование файлового ресурса в Linux](storage-how-to-use-files-linux.md)
* [Подключите и подключите общую папку на macOS](storage-how-to-use-files-mac.md)

Дополнительные сведения о службе файлов Azure см. по следующим ссылкам.

* [Часто задаваемые вопросы о файлах хранилища](storage-files-faq.md)
* [Устранение неполадок хранилища файлов Azure в Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Устранение неполадок в работе хранилища файлов Azure в Linux](storage-troubleshoot-linux-file-connection-problems.md)
