---
title: Настройка брандмауэров службы хранилища Azure и виртуальных сетей | Документация Майкрософт
description: Настройте многоуровневую сетевую безопасность для своей учетной записи.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: 6d6ca1fe1256f1571079027ebd299492bfa62f41
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057393"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Настройка брандмауэров службы хранилища Azure и виртуальных сетей

Служба хранилища Azure предоставляет многоуровневую модель безопасности. Эта модель обеспечивает безопасность учетных записей хранения, открывая доступ к ним только по определенным поддерживаемым сетям. После настройки правил сети доступ к учетной записи хранения могут получать только приложения, запрашивающие данные из этого ограниченного числа сетей.

При этом приложению, получающему доступ к учетной записи хранения с активными правилами сети, необходима надлежащая авторизация. Авторизация поддерживается с учетными данными Azure Active Directory (Azure AD) для больших двоичных объектов и очередей, допустимый ключ доступа учетной записи или маркером SAS.

> [!IMPORTANT]
> Включение правил брандмауэра для учетной записи хранения блокирует доступ для входящих запросов к данным по умолчанию, за исключением запросов от служб, работающих внутри виртуальной сети Azure (VNet). Запросы от других служб Azure, в том числе портала Azure, служб метрики и ведения журналов, блокируются.
>
> Вы можете предоставить доступ службам Azure, работающим изнутри виртуальной сети, за счет подсети экземпляра службы. Разрешите небольшое число сценариев, используя механизм [исключений](#exceptions), описанный в разделе ниже. Доступ к порталу Azure возможен только с компьютера, находящегося в пределах настроенной доверенной границы (на основе протокола IP или VNet).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Сценарии

Настройте в учетных записях хранения запрет доступа для трафика из всех сетей (включая интернет-трафик) по умолчанию. Затем предоставьте доступ для трафика из определенных виртуальных сетей. Такая конфигурация позволяет создать для приложений границу в виде безопасной сети. Вы также можете разрешить доступ для диапазона общедоступных IP-адресов в Интернете, позволив устанавливать подключения из конкретных локальных или интернет-клиентов.

В службе хранилища Azure сетевые правила применяются ко всем сетевым протоколам, включая REST и SMB. Для доступа к данным с помощью таких инструментов, как портал Azure, Обозреватель службы хранилища и AZCopy, требуются явные правила сети.

Правила сети можно применить к уже имеющимся учетным записям хранения или к новым во время их создания.

Как только правила сети применены, они распространяются на все запросы. Токены SAS, позволяющие получить доступ к конкретному IP-адресу, служат для ограничения доступа владельца токена, а не предоставляют доступ к каким-либо новым ресурсам, не указанным в настроенных сетевых правилах.

Правила сети не влияют на трафик дисков виртуальных машин (включая операции подключения и отключения, а также дисковые операции ввода-вывода). Сетевые правила обеспечивают безопасность во время доступа REST к страничным BLOB-объектам.

Классические учетные записи хранения не поддерживают брандмауэры и виртуальные сети.

Чтобы использовать неуправляемые диски в учетных записях хранения с действующими правилами сети относительно резервного копирования и восстановления виртуальных машин, необходимо создать исключение. Эта процедура описана в разделе [Исключения](#exceptions) данной статьи. Исключения брандмауэра не применяются к управляемым дискам, так как ими уже управляет Azure.

## <a name="change-the-default-network-access-rule"></a>Изменение сетевого правила доступа по умолчанию

По умолчанию учетные записи хранения принимают запросы на подключение от клиентов в сети. Для ограничения доступа из выбранных сетей необходимо сначала изменить действие по умолчанию.

> [!WARNING]
> Изменение сетевых правил может повлиять на возможность подключения приложений к службе хранилища Azure. Когда для сетевого правила по умолчанию указано значение **deny**, доступ к данным блокируется полностью, если не применяются конкретные сетевые правила, **разрешающие** доступ. Предоставьте доступ для разрешенных сетей с помощью сетевых правил, прежде чем изменить правила по умолчанию и запретить доступ.

### <a name="managing-default-network-access-rules"></a>Управление сетевыми правилами доступа по умолчанию

Вы можете управлять сетевыми правилами доступа по умолчанию для учетных записей хранения с помощью портала Azure, PowerShell или CLI версии 2.

#### <a name="azure-portal"></a>Портал Azure

1. Перейдите к учетной записи хранения, которую нужно защитить.

1. Щелкните меню параметров **Брандмауэры и виртуальные сети**.

1. Чтобы запретить доступ по умолчанию, разрешите доступ в разделе **Выбранные сети**. Чтобы разрешить передачу трафика из всех сетей, разрешите доступ в разделе **Все сети**.

1. Щелкните **Сохранить**, чтобы применить изменения.

#### <a name="powershell"></a>PowerShell

1. Установите [Azure PowerShell](/powershell/azure/install-Az-ps) и [выполните вход](/powershell/azure/authenticate-azureps).

1. Отобразите состояние правила по умолчанию для учетной записи хранения.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Настройте в правиле по умолчанию запрет сетевого доступа по умолчанию.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Настройте в правиле по умолчанию разрешение сетевого доступа по умолчанию.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLI 2.0

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli).

1. Отобразите состояние правила по умолчанию для учетной записи хранения.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Настройте в правиле по умолчанию запрет сетевого доступа по умолчанию.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Настройте в правиле по умолчанию разрешение сетевого доступа по умолчанию.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Предоставление доступа из виртуальной сети

Вы можете настроить учетные записи хранения таким образом, чтобы они разрешали доступ только из определенных виртуальных сетей.

Включите [конечную точку службы](/azure/virtual-network/virtual-network-service-endpoints-overview) для службы хранилища Azure, входящей в нужную виртуальную сеть. Эта конечная точка обеспечит оптимальный маршрут трафика к службе хранилища Azure. В каждом запросе также передаются удостоверения виртуальной сети и подсети. Потом администраторы настроят сетевые правила для учетной записи хранения, разрешающие получение запросов из определенных подсетей в виртуальной сети. Клиенты, которым предоставлен доступ по этим сетевым правилам, по прежнему должны выполнять требования авторизации учетной записи хранения, чтобы получать доступ к данным.

Каждая учетная запись хранения поддерживает до 100 правил виртуальной сети, которые можно объединить с [правилами IP-сети](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Доступные регионы виртуальной сети

Как правило, конечные точки служб работают между виртуальными сетями и экземплярами служб в одном регионе Azure. При использовании конечных точек служб со службой хранилища Azure область применения расширяется и включает [связанный регион](/azure/best-practices-availability-paired-regions). Конечные точки служб обеспечивают непрерывность работы при отработке регионального отказа, а также доступ на чтение к экземплярам геоизбыточного хранилища (RA-GRS). Правила сети, предоставляющие доступ к учетной записи хранения из виртуальной сети, также предоставляют доступ к любому экземпляру RA-GRS.

Планируя аварийное восстановление на случай регионального сбоя, следует заранее создать виртуальные сети в связанном регионе. Включите конечные точки для службы хранилища Azure с применением сетевых правил, разрешающих доступ из этих альтернативных виртуальных сетей. Затем примените эти правила к учетным записям своего геоизбыточного хранилища.

> [!NOTE]
> Конечные точки службы не применяются к трафику за пределами региона виртуальной сети и заданного связанного региона. Сетевые правила, предоставляющие доступ к учетным записям хранения из виртуальных сетей, применяются только в основном регионе учетной записи хранения или в заданном связанном регионе.

### <a name="required-permissions"></a>Необходимые разрешения

Чтобы применить правило виртуальной сети к учетной записи хранения, у пользователя должны быть соответствующие разрешения для добавляемых подсетей. Это разрешение *Join Service to a Subnet* (Добавить службу к подсети), включенное во встроенную роль *Участник учетных записей хранения*. Его также можно добавить к определениям пользовательских ролей.

Учетная запись хранения и виртуальные сети, которым предоставлен доступ, могут находиться в разных подписках, но эти подписки должны быть частью одного клиента Azure AD.

### <a name="managing-virtual-network-rules"></a>Управление правилами для виртуальных сетей

Правилами виртуальных сетей для учетных записей хранения можно управлять с помощью портала Azure, PowerShell или CLI версии 2.

#### <a name="azure-portal"></a>Портал Azure

1. Перейдите к учетной записи хранения, которую нужно защитить.

1. Щелкните меню параметров **Брандмауэры и виртуальные сети**.

1. Убедитесь, что вы разрешили доступ в разделе **Выбранные сети**.

1. Чтобы предоставить доступ виртуальной сети с новым сетевым правилом, в разделе **Виртуальные сети** выберите команду **Добавить существующую виртуальную сеть**, укажите параметры для пунктов **Виртуальные сети** и **Подсети**, а затем нажмите **Добавить**. Чтобы создать виртуальную сеть и предоставить ей доступ, выберите команду **Добавить новую виртуальную сеть**. Укажите сведения, необходимые для создания виртуальной сети, а затем нажмите **Создать**.

    > [!NOTE]
    > Если конечная точка службы для службы хранилища Azure еще не настроена для выбранной виртуальной сети и подсетей, ее можно настроить в ходе этой операции.

1. Если нужно удалить правила виртуальной сети или подсети, щелкните **...**, чтобы открыть контекстное меню для виртуальной сети или подсети, и выберите пункт **Удалить**.

1. Щелкните **Сохранить**, чтобы применить изменения.

#### <a name="powershell"></a>PowerShell

1. Установите [Azure PowerShell](/powershell/azure/install-Az-ps) и [выполните вход](/powershell/azure/authenticate-azureps).

1. Выведите список правил для виртуальной сети.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Включите конечную точку службы для службы хранилища Azure в имеющейся виртуальной сети и подсети.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Добавьте сетевое правило для виртуальной сети и подсети.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Удалите сетевое правило для виртуальной сети и подсети.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Обязательно [укажите для правила по умолчанию](#change-the-default-network-access-rule) значение **deny**, иначе сетевые правила не будут действовать.

#### <a name="cliv2"></a>CLI 2.0

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli).

1. Выведите список правил для виртуальной сети.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Включите конечную точку службы для службы хранилища Azure в имеющейся виртуальной сети и подсети.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Добавьте сетевое правило для виртуальной сети и подсети.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Удалите сетевое правило для виртуальной сети и подсети.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Обязательно [укажите для правила по умолчанию](#change-the-default-network-access-rule) значение **deny**, иначе сетевые правила не будут действовать.

## <a name="grant-access-from-an-internet-ip-range"></a>Предоставление доступа из диапазона IP-адресов в Интернете

Вы можете настроить учетные записи хранения так, чтобы разрешить доступ из определенных диапазонов общедоступных IP-адресов в Интернете. Такая конфигурация позволяет предоставить доступ конкретным интернет-службам и локальным сетям, заблокировав при этом общий интернет-трафик.

Предоставьте разрешенные диапазоны адресов в Интернете, используя [нотацию CIDR](https://tools.ietf.org/html/rfc4632) в формате *16.17.18.0/24* или в виде отдельных IP-адресов, таких как *16.17.18.19*.

   > [!NOTE]
   > Небольшие адреса, использующие размеры с префиксом /31 или /32, не поддерживаются. Эти диапазоны следует настраивать с помощью отдельных правил для IP-адресов.

Правила IP-сети можно применять только для **общедоступных** IP-адресов в Интернете. Диапазоны IP-адресов, зарезервированные для частных сетей (как определено в документе [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)), запрещено использовать в правилах IP. К частным сетям относятся адреса, начинающиеся с _10.*_, _172.16.*_ - _172.31.*_ и _192.168.*_.

   > [!NOTE]
   > Правила IP-сети не затрагивают запросы, исходящие из того же региона Azure, к которому относится учетная запись хранения. Используйте [правила виртуальной сети](#grant-access-from-a-virtual-network) для разрешения запросов из того же региона.

Сейчас поддерживаются только IPV4-адреса.

Каждая учетная запись хранения поддерживает до 100 правил IP-сети, которые можно объединить с [правилами виртуальной сети](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Настройка доступа из локальных сетей

Чтобы предоставить доступ из вашей локальной сети к учетной записи хранения в правиле IP-сети, необходимо определить IP-адреса для Интернета, которые используются в вашей сети. За помощью обращайтесь к администратору сети.

Если вы используете [ExpressRoute](/azure/expressroute/expressroute-introduction) для локальной среды, для общедоступного пиринга или пиринга Майкрософт, то вам необходимо определить используемые IP-адреса NAT. Для общедоступного пиринга в каждом канале ExpressRoute по умолчанию используется два IP-адреса NAT для трафика служб Azure, когда он входит в основную магистральную сеть Microsoft Azure. Для пиринга Майкрософт используются IP-адреса NAT, предоставленные клиентом или поставщиком услуг. Чтобы разрешить доступ к ресурсам служб, необходимо разрешить эти общедоступные IP-адреса в настройках брандмауэра IP-адресов ресурсов. Чтобы найти IP-адреса канала ExpressRoute для общедоступного пиринга, [отправьте запрос по ExpressRoute в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) через портал Azure. Узнайте больше о [NAT для общедоступного пиринга и пиринга Майкрософт](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>Управление правилами IP-сети

Правилами IP-сети для учетных записей хранения можно управлять с помощью портала Azure, PowerShell или CLI версии 2.

#### <a name="azure-portal"></a>Портал Azure

1. Перейдите к учетной записи хранения, которую нужно защитить.

1. Щелкните меню параметров **Брандмауэры и виртуальные сети**.

1. Убедитесь, что вы разрешили доступ в разделе **Выбранные сети**.

1. Чтобы предоставить доступ к диапазону IP-адресов в Интернете, введите IP-адрес или диапазон адресов (в формате CIDR) в разделе **Брандмауэр** > **Диапазон адресов**.

1. Чтобы удалить правило IP-сети, щелкните значок корзины рядом с диапазоном адресов.

1. Щелкните **Сохранить**, чтобы применить изменения.

#### <a name="powershell"></a>PowerShell

1. Установите [Azure PowerShell](/powershell/azure/install-Az-ps) и [выполните вход](/powershell/azure/authenticate-azureps).

1. Выведите список правил IP-сети.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Добавьте правило сети для отдельного IP-адреса.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Добавьте правило сети для диапазона IP-адресов.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Удалите правило сети для отдельного IP-адреса.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Удалите правило сети для диапазона IP-адресов.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Обязательно [укажите для правила по умолчанию](#change-the-default-network-access-rule) значение **deny**, иначе сетевые правила не будут действовать.

#### <a name="cliv2"></a>CLI 2.0

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli).

1. Выведите список правил IP-сети.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Добавьте правило сети для отдельного IP-адреса.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Добавьте правило сети для диапазона IP-адресов.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Удалите правило сети для отдельного IP-адреса.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Удалите правило сети для диапазона IP-адресов.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Обязательно [укажите для правила по умолчанию](#change-the-default-network-access-rule) значение **deny**, иначе сетевые правила не будут действовать.

## <a name="exceptions"></a>Исключения

Правила сети могут обеспечить безопасную конфигурацию сети для большинства сценариев. Однако в некоторых случаях нужны исключения, чтобы воспользоваться всеми доступными функциями. В учетных записях хранения можно настроить исключения для доверенных служб Майкрософт, а также для доступа к данным аналитики хранилища.

### <a name="trusted-microsoft-services"></a>Доверенные службы Майкрософт

Некоторые службы Майкрософт, взаимодействующие с учетными записями хранения, работают из сетей, которым нельзя предоставить доступ через сетевые правила.

Чтобы помочь таким службам работать как нужно, разрешите доверенным службам Майкрософт обходить сетевые правила. Эти службы будут использовать строгую проверку подлинности для доступа к учетной записи хранения.

При активном исключении **Разрешить доверенным службам Майкрософт...** доступ к учетной записи хранения предоставляется таким службам (при регистрации в вашей подписке):

|Service|Имя поставщика ресурсов|Назначение|
|:------|:---------------------|:------|
|Служба архивации Azure|Microsoft.Backup|Резервное копирование и восстановление неуправляемых дисков в виртуальных машинах IAAS. (Не требуется для управляемых дисков.) [Узнайте больше](/azure/backup/backup-introduction-to-azure-backup).|
|Azure Data Box|Microsoft.DataBox|Включает Импорт данных в Azure с помощью Data Box. [Узнайте больше](/azure/databox/data-box-overview).|
|Azure DevTest Labs|Microsoft.DevTestLab|Создание пользовательских образов и установка артефактов. [Узнайте больше](/azure/devtest-lab/devtest-lab-overview).|
|Сетка событий Azure|Microsoft.EventGrid|Включение публикации событий в хранилище BLOB-объектов и предоставление службе "Сетка событий" разрешения на публикацию в хранилище очередей. См. дополнительные сведения о [событиях хранилища BLOB-объектов](/azure/event-grid/event-sources) и [публикации в хранилище очередей](/azure/event-grid/event-handlers).|
|Центры событий Azure|Microsoft.EventHub|Архивация данных с помощью функции "Сбор" в Центрах событий. [Подробнее](/azure/event-hubs/event-hubs-capture-overview)|
|Azure HDInsight|Microsoft.HDInsight|Подготовьте исходные данные в файловой системы по умолчанию для нового кластера HDInsight. [Узнайте больше](https://azure.microsoft.com/en-us/blog/enhance-hdinsight-security-with-service-endpoints/).|
|Azure Monitor|Microsoft.Insights|Позволяет записывать данные мониторинга в защищенную учетную запись. [Дополнительные сведения](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security).|
|Сеть Azure|Microsoft.Networking|Хранение и анализ журналов сетевого трафика. [Узнайте больше](/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure Site Recovery|Microsoft.SiteRecovery |Настройка аварийного восстановления путем включения репликации для виртуальных машин Azure IaaS. Эта процедура необходима при использовании учетных записей хранения кэша, источника или целевой учетной записи хранения со включенным брандмауэром.  [Узнайте больше](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication).|
|Хранилище данных SQL Azure|Microsoft.Sql|Позволяет импортировать и экспортировать сценарии с использованием PolyBase. [Узнайте больше](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).|

### <a name="storage-analytics-data-access"></a>Доступ к данным аналитики хранилища

В некоторых случаях доступ для чтения журналов диагностики и метрик нужно получать за пределами сети. Вы можете предоставить исключения из правил сети, чтобы разрешить доступ на чтение к файлам журналов учетной записи хранения, таблицам метрик или к обоим ресурсам. [Дополнительные сведения о работе с аналитикой хранилища.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Управление исключениями

Исключениями из правил сети можно управлять с помощью портала Azure, PowerShell или Azure CLI v2.

#### <a name="azure-portal"></a>Портал Azure

1. Перейдите к учетной записи хранения, которую нужно защитить.

1. Щелкните меню параметров **Брандмауэры и виртуальные сети**.

1. Убедитесь, что вы разрешили доступ в разделе **Выбранные сети**.

1. В разделе **Исключения** выберите те исключения, которые нужно предоставить.

1. Щелкните **Сохранить**, чтобы применить изменения.

#### <a name="powershell"></a>PowerShell

1. Установите [Azure PowerShell](/powershell/azure/install-Az-ps) и [выполните вход](/powershell/azure/authenticate-azureps).

1. Отобразите исключения для сетевых правил учетной записи хранения.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Настройте исключения для сетевых правил учетной записи хранения.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Удалите исключения для сетевых правил учетной записи хранения.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Обязательно [укажите для правила по умолчанию](#change-the-default-network-access-rule) значение **deny**, иначе исключение не будет действовать.

#### <a name="cliv2"></a>CLI 2.0

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli).

1. Отобразите исключения для сетевых правил учетной записи хранения.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Настройте исключения для сетевых правил учетной записи хранения.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Удалите исключения для сетевых правил учетной записи хранения.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Обязательно [укажите для правила по умолчанию](#change-the-default-network-access-rule) значение **deny**, иначе исключение не будет действовать.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о конечных точках службы сети Azure см. в статье [Конечные точки службы виртуальной сети](/azure/virtual-network/virtual-network-service-endpoints-overview).

Более подробную информацию о безопасности службы хранилища Azure см. в статье [Руководство по безопасности службы хранилища Azure](storage-security-guide.md).
