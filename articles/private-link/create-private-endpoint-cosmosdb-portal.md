---
title: Connect to an Azure Cosmos account with Azure Private Link
description: Learn how to securely access the Azure Cosmos account from a VM by creating a Private Endpoint.
author: asudbring
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 90710176ec16d1c565e24ff7df56b0b838f2699e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229418"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Connect privately to an Azure Cosmos account using Azure Private Link

Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this article, you will learn how to create a VM on an Azure virtual network and an Azure Cosmos account with a Private Endpoint using the Azure portal. Then, you can securely access the Azure Cosmos account from the VM.

## <a name="sign-in-to-azure"></a>Войдите в Azure

Sign in to the [Azure portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Создание ВМ

### <a name="create-the-virtual-network"></a>Создание виртуальной сети

In this section, you will create a virtual network and the subnet to host the VM that is used to access your Private Link resource (an Azure Cosmos account in this example).

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

### <a name="create-the-virtual-machine"></a>Создание виртуальной машины

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
    | Имя пользователя | Enter a username of your choice. |
    | Пароль | Введите выбранный пароль. Пароль должен включать минимум 12 символов и соответствовать [определенным требованиям к сложности](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Подтверждение пароля | Reenter the password. |
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

## <a name="create-an-azure-cosmos-account"></a>Создание учетной записи Azure Cosmos

Create an [Azure Cosmos SQL API account](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). For simplicity, you can create the Azure Cosmos account in the same region as the other resources (that is "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Create a Private Endpoint for your Azure Cosmos account

Create a Private Link for your Azure Cosmos account as described in the [Create a Private Link using the Azure portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) section of the linked article.

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Connect to the VM *myVm* from the internet as follows:

1. На портале в строке поиска введите *myVm*.

1. Нажмите кнопку **Подключиться**. После нажатия кнопки **Подключиться** откроется окно **Connect to virtual machine** (Подключение к виртуальной машине).

1. Щелкните **Скачать RDP-файл**. Azure создаст и скачает на ваш компьютер файл протокола удаленного рабочего стола (*RDP*).

1. Откройте скачанный файл *RDP*.

    1. При появлении запроса выберите **Подключиться**.

    1. Введите имя пользователя и пароль, указанные при создании виртуальной машины.

        > [!NOTE]
        > Возможно, потребуется выбрать **More choices** > **Use a different account** (Дополнительные варианты > Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины.

1. Нажмите кнопку **ОК**.

1. При входе в систему может появиться предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

1. Когда появится рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Access the Azure Cosmos account privately from the VM

In this section, you will connect privately to the Azure Cosmos account using the Private Endpoint. 

> [!IMPORTANT]
> The DNS configuration for the Azure Cosmos account needs a manual modification on the hosts file to include the FQDN of the specific account. In production scenarios you will configure the DNS server to use the private IP addresses. However for the demo purpose, you can use administrator permissions on the VM and modify the `c:\Windows\System32\Drivers\etc\hosts` file (on Windows) or `/etc/hosts` file (on Linux) to include the IP address and DNS mapping.

1. To include the IP address and DNS mapping, sign into your Virtual machine *myVM*, open the `c:\Windows\System32\Drivers\etc\hosts` file and include the DNS information from previous step in the following format:

   [Private IP Address] [Account endpoint].documents.azure.com

   **Пример.**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. In the Remote Desktop of *myVM*, install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Select **Cosmos DB Accounts (Preview)** with the right-click.

1. Select **Connect to Cosmos DB**.

1. Выберите **API**.

1. Enter the connection string by pasting the information previously copied.

1. Щелкните **Далее**.

1. Нажмите кнопку **Подключиться**.

1. Browse the Azure Cosmos databases and containers from *mycosmosaccount*.

1. (Optionally) add new items to *mycosmosaccount*.

1. Close the remote desktop connection to *myVM*.

## <a name="clean-up-resources"></a>Очистка ресурсов

When you're done using the Private Endpoint, Azure Cosmos account and the VM, delete the resource group and all of the resources it contains: 

1. Введите *myResourceGroup* в поле **Поиск** в верхней части портала и выберите *myResourceGroup* в результатах поиска.

1. Выберите **Удалить группу ресурсов**.

1. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>Дальнейшие действия

In this article, you created a VM on a virtual network, an Azure Cosmos account and a Private Endpoint. You connected to the VM from the internet and securely communicated to the Azure Cosmos account using Private Link.

* To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).

* To learn more about limitation of Private Endpoint when using with Azure Cosmos DB, see [Azure Private Link with Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) article.