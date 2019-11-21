---
title: Connect privately to a storage account using Azure Private Endpoint
description: Learn how to connect privately to a storage account in Azure using a Private Endpoint.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfe0caaf199821358f8a66ac65ae75c38336c725
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228093"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connect privately to a storage account using Azure Private Endpoint
Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this Quickstart, you will learn how to create a VM on an Azure virtual network, a storage account with a Private Endpoint using the Azure portal. Then, you can securely access the storage account from the VM.


## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-vm"></a>Создание ВМ
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link Resource (a storage account in this example).

### <a name="create-the-virtual-network"></a>Создание виртуальной сети

In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource.

1. Вверху с левой стороны экрана последовательно выберите **Создать ресурс** > **Сети** > **Виртуальная сеть**.
1. В подменю **Создать виртуальную сеть** введите или выберите следующую информацию:

    | Параметр | Value |
    | ------- | ----- |
    | Name | Введите *myVirtualNetwork*. |
    | Пространство адресов | Введите *10.1.0.0/16*. |
    | Subscription | Выберите свою подписку.|
    | группа ресурсов. | Выберите **Создать**, а затем введите *myResourceGroup* и нажмите кнопку **ОК**. |
    | Location | Выберите **WestCentralUS**.|
    | Имя подсети | Введите *mySubnet*. |
    | Диапазон адреса подсети | Введите *10.1.0.0/24*. |
    |||
1. Оставьте без изменений значения остальных параметров и выберите **Создать**.


### <a name="create-virtual-machine"></a>Создание виртуальной машины

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual machine**.

1. В окне **Создание виртуальной машины — Основы** введите или выберите следующую информацию:

    | Параметр | Value |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | Subscription | Выберите свою подписку. |
    | группа ресурсов. | Выберите **myResourceGroup**. Вы создали ее в предыдущем разделе.  |
    | **Подробности об экземпляре** |  |
    | Имя виртуальной машины | Введите *myVm*. |
    | Регион | Выберите **WestCentralUS**. |
    | Параметры доступности | Оставьте значение по умолчанию **No infrastructure redundancy required** (Избыточность инфраструктуры не требуется). |
    | Изображение | Выберите **Центр обработки данных Windows Server 2019**. |
    | Размер | Оставьте значение по умолчанию **Standard DS1 v2**. |
    | **Учетная запись администратора** |  |
    | Имя пользователя | Введите выбранное имя пользователя. |
    | Пароль | Введите выбранный пароль. Пароль должен включать минимум 12 символов и соответствовать [определенным требованиям к сложности](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Подтверждение пароля | Введите пароль еще раз. |
    | **Правила входящего порта** |  |
    | Общедоступные входящие порты | Оставьте значение по умолчанию **Отсутствует**. |
    | **Экономия** |  |
    | Already have a Windows license? (У вас уже есть лицензия Windows?) | Оставьте значение по умолчанию **Нет**. |
    |||

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. В окне **Создание виртуальной машины — Сети** выберите такую информацию:

    | Параметр | Value |
    | ------- | ----- |
    | Виртуальная сеть | Оставьте значение по умолчанию **myVirtualNetwork**.  |
    | Пространство адресов | Оставьте значение по умолчанию **10.1.0.0/24**.|
    | Подсеть | Оставьте значение по умолчанию **mySubnet (10.1.0.0/24)** .|
    | Общедоступный IP-адрес | Оставьте значение по умолчанию **(new) myVm-ip**. |
    | Общедоступные входящие порты | Выберите **Разрешить выбранные порты**. |
    | Выбрать входящие порты | Выберите **HTTP** и **RDP**.|
    ||

1. Выберите **Review + create** (Просмотреть и создать). Вы будете перенаправлены на страницу **Просмотр и создание**, где Azure проверит вашу конфигурацию.

1. При появлении сообщения **Проверка пройдена** нажмите кнопку **Создать**.

## <a name="create-your-private-endpoint"></a>Create your Private Endpoint
In this section, you will create a private storage account using a Private Endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Storage** > **Storage account**.

1. In **Create storage account - Basics**, enter or select this information:

    | Параметр | Value |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | Subscription | Выберите свою подписку. |
    | группа ресурсов. | Выберите **myResourceGroup**. Вы создали ее в предыдущем разделе.|
    | **Подробности об экземпляре** |  |
    | Имя учетной записи хранения  | Enter *mystorageaccount*. Если это имя используется, создайте уникальное имя. |
    | Регион | Выберите **WestCentralUS**. |
    | Ориентированное на производительность| Оставьте значение по умолчанию **Стандартная**. |
    | Тип учетной записи | Leave the default **Storage (general purpose v2)** . |
    | Репликация | Select **Read-access geo-redundant storage (RA-GRS)** . |
    |||
  
3. Select **Next: Networking**.
4. In **Create a storage account - Networking**, connectivity method, select **Private Endpoint**.
5. In **Create a storage account - Networking**, select **Add Private Endpoint**. 
6. In **Create Private Endpoint**, enter or select this information:

    | Параметр | Value |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | Subscription | Выберите свою подписку. |
    | группа ресурсов. | Выберите **myResourceGroup**. Вы создали ее в предыдущем разделе.|
    |Location|Выберите **WestCentralUS**.|
    |Name|Enter *myPrivateEndpoint*.  |
    |Storage sub-resource|Leave the default **Blob**. |
    | **СЕТИ** |  |
    | Виртуальная сеть  | Select *MyVirtualNetwork* from resource group *myResourceGroup*. |
    | Подсеть | Выберите *mySubnet*. |
    | **ЧАСТНАЯ ИНТЕГРАЦИЯ DNS**|  |
    | Интеграция с частной зоной DNS  | Leave the default **Yes**. |
    | Частная зона DNS  | Leave the default ** (New) privatelink.blob.core.windows.net**. |
    |||
7. Нажмите кнопку **ОК**. 
8. Выберите **Review + create** (Просмотреть и создать). Вы будете перенаправлены на страницу **Просмотр и создание**, где Azure проверит вашу конфигурацию. 
9. При появлении сообщения **Проверка пройдена** нажмите кнопку **Создать**. 
10. Browse to the storage account resource that you just created.
11. Select **Access Keys** from the left content menu.
12. Select **Copy** on the connection string for key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Connect to the VM *myVm* from the internet as follows:

1. На портале в строке поиска введите *myVm*.

1. Нажмите кнопку **Подключиться**. После нажатия кнопки **Подключиться** откроется окно **Connect to virtual machine** (Подключение к виртуальной машине).

1. Щелкните **Скачать RDP-файл**. Azure создаст и скачает на ваш компьютер файл протокола удаленного рабочего стола (*RDP*).

1. Откройте скачанный RDP-файл*.

    1. При появлении запроса выберите **Подключиться**.

    1. Введите имя пользователя и пароль, указанные при создании виртуальной машины.

        > [!NOTE]
        > Возможно, потребуется выбрать **More choices** > **Use a different account** (Дополнительные варианты > Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины.

1. Нажмите кнопку **ОК**.

1. При входе в систему может появиться предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

1. Когда появится рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.  

## <a name="access-storage-account-privately-from-the-vm"></a>Access storage account privately from the VM

In this section, you will connect privately to the storage account using the Private Endpoint.

> [!IMPORTANT]
> DNS configuration for storage needs a manual modification on the hosts file to include the FQDN of the specific account Please modify the following file using administrator permissions on Windows: c:\Windows\System32\Drivers\etc\hosts or Linux /etc/hosts Include the DNS information for the account from previous step in the following format [Private IP Address] myaccount.blob.core.windows.net

1. На удаленном рабочем столе  *myVm* откройте PowerShell.
2. Enter `nslookup mystorageaccount.blob.core.windows.net` You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Установите [Обозреватель службы хранилища Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Щелкните **Далее**.
8. Enter the connection string by pasting the information previously copied.
9. Щелкните **Далее**.
10. Нажмите кнопку **Подключиться**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. С помощью AzCopy можно передавать данные из хранилища BLOB-объектов, таблиц и файлов и обратно. 


## <a name="clean-up-resources"></a>Очистка ресурсов 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Введите *myResourceGroup* в поле **Поиск** в верхней части портала и выберите *myResourceGroup* в результатах поиска. 
2. Выберите **Удалить группу ресурсов**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## <a name="next-steps"></a>Дальнейшие действия
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
