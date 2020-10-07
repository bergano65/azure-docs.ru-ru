---
title: Краткое руководство. Создание частной конечной точки Azure с помощью Azure CLI
description: В этом кратком руководстве содержатся сведения о частной конечной точке Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e7c098ba06086781306960f76978aac9e4fa06bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87502670"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Краткое руководство. Создание частной конечной точки с помощью Azure CLI

Частная конечная точка — ключевой компонент для построения частной ссылки в Azure. Это позволяет ресурсам Azure, таким как виртуальные машины (ВМ), обмениваться данными с ресурсами частной ссылки в частном порядке. В этом кратком руководстве вы узнаете, как создать виртуальную машину в виртуальной сети и сервер базы данных SQL с частной конечной точкой, используя Azure CLI. После этого вы сможете получить доступ к виртуальной машине и получить безопасный доступ к ресурсу частной ссылки (в этом примере это частный сервер базы данных SQL).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для выполнения инструкций из этого руководства вам потребуется использовать Azure CLI 2.0.28 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Перед созданием любого ресурса необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). В этом примере создается группа ресурсов с именем *myResourceGroup* в расположении *westcentralus*.

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Создайте виртуальную сеть

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet). В этом примере создается виртуальная сеть по умолчанию с именем *myVirtualNetwork* с подсетью *mySubnet*.

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Отключение политик подсети частной конечной точки

Поскольку Azure развертывает ресурсы в подсеть виртуальной сети, чтобы отключить политики сети частной конечной точки, вам следует создать или обновить подсеть. Обновите конфигурацию подсети *mySubnet* с помощью команды [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```

## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды az vm create. При появлении запроса укажите пароль в качестве учетных данных для входа на виртуальную машину. В этом примере создается виртуальная машина с именем *myVM*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

Запишите общедоступный IP-адрес виртуальной машины. Этот адрес используется на следующем шаге, чтобы подключиться к виртуальной машине из Интернета.

## <a name="create-a-server-in-sql-database"></a>Создание сервера базы данных SQL

Создайте сервер базы данных SQL с помощью команды az sql server create. Помните, что имя сервера должно быть уникальным в пределах Azure, поэтому замените значение заполнителя в квадратных скобках своим уникальным значением:

```azurecli-interactive
# Create a server in the resource group
az sql server create \
    --name "myserver"\
    --resource-group myResourceGroup \
    --location WestUS \
    --admin-user "sqladmin" \
    --admin-password "CHANGE_PASSWORD_1"

# Create a database in the server with zone redundancy as false
az sql db create \
    --resource-group myResourceGroup  \
    --server myserver \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen4 \
    --capacity 1
```

Идентификатор сервера аналогичный  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.```. Вы будете использовать идентификатор сервера в следующем шаге.

## <a name="create-the-private-endpoint"></a>Создание частной конечной точки

Создайте частную конечную точку для логического сервера SQL в виртуальной сети:

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS

Создайте частную зону DNS для домена базы данных SQL, создайте ассоциированную связь с виртуальной сетью и создайте группу зоны DNS, чтобы связать частную конечную точку с частной зоной DNS. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \
   --name  "privatelink.database.windows.net"
az network private-dns link vnet create --resource-group myResourceGroup \
   --zone-name  "privatelink.database.windows.net"\
   --name MyDNSLink \
   --virtual-network myVirtualNetwork \
   --registration-enabled false
az network private-endpoint dns-zone-group create \
   --resource-group myResourceGroup \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Подключитесь к виртуальной машине *myVm* из Интернета, выполнив следующие действия.

1. На портале в строке поиска введите *myVm*.

1. Нажмите кнопку **Подключиться**. После нажатия кнопки **Подключиться** откроется окно **Connect to virtual machine** (Подключение к виртуальной машине).

1. Щелкните **Скачать RDP-файл**. Azure создаст и скачает на ваш компьютер файл протокола удаленного рабочего стола (*RDP*).

1. Откройте скачанный RDP-файл*.

    1. При появлении запроса выберите **Подключиться**.

    1. Введите имя пользователя и пароль, указанные при создании виртуальной машины.

        > [!NOTE]
        > Возможно, потребуется выбрать **More choices** > **Use a different account** (Дополнительные варианты > Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины.

1. Щелкните **ОК**.

1. При входе в систему может появиться предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

1. Когда появится рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.  

## <a name="access-sql-database-privately-from-the-vm"></a>Доступ с виртуальной машины к базе данных SQL в частном порядке

В этом разделе вы будете подключаться к базе данных SQL из виртуальной машины с помощью частной конечной точки.

1. Откройте PowerShell на удаленном рабочем столе *myVm*.
2. Введите myserver.database.windows.net

   Должно появиться сообщение следующего вида:

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. Установите SQL Server Management Studio
4. В окне "Подключение к серверу" введите или выберите приведенные ниже сведения.

   - Тип сервера: Выберите "Ядро СУБД".
   - Имя сервера: Выберите myserver.database.windows.net
   - Имя пользователя: Введите имя пользователя, указанное во время создания.
   - Пароль: Введите пароль, указанный во время создания.
   - Запомнить пароль: Выберите "Да".

5. Выберите **Подключиться**.
6. В меню слева выберите **Базы данных**.
7. (Дополнительно) Создание или запрос информации из базы данных *mydatabase*
8. Закройте подключение к удаленному рабочему столу *myVm*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполните команду "az group delete".

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о службе [Приватный канал Azure](private-link-overview.md)
