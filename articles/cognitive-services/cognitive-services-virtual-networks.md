---
title: Виртуальные сети
titleSuffix: Azure Cognitive Services
description: Настройка многоуровневой сетевой безопасности для ресурсов Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1ae3caa2d1f90bbbae1070d95d676eb206a361a0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647346"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Настройка виртуальных сетей Azure Cognitive Services

Azure Cognitive Services предоставляет многоуровневую модель безопасности. Эта модель позволяет защищать учетные записи Cognitive Services в определенном подмножестве сетей. При настройке сетевых правил только приложения, запрашивающие данные через указанный набор сетей, могут получить доступ к учетной записи. Вы можете ограничить доступ к ресурсам с помощью фильтрации запросов. Разрешение только запросов, исходящих от указанных IP-адресов, диапазонов IP-адреса или списка подсетей в [виртуальных сетях Azure](../virtual-network/virtual-networks-overview.md). Если вы заинтересованы в этом предложении, вам потребуется [запросить предварительный доступ](https://aka.ms/cog-svc-vnet-signup).

Приложение, обращающееся к ресурсу Cognitive Services, когда действуют правила сети, требует авторизации. Авторизация поддерживается с учетными данными [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) или с помощью допустимого ключа API.

> [!IMPORTANT]
> Включение правил брандмауэра для учетной записи Cognitive Services блокирует входящие запросы данных по умолчанию. Чтобы разрешить запросы по, необходимо выполнить одно из следующих условий.
> * Запрос должен исходить из службы, работающей в виртуальной сети Azure (VNet), в списке разрешенных подсетей целевой Cognitive Services учетной записи. Конечная точка в запросах, отправленных из виртуальной сети, должна быть задана в качестве [пользовательского поддомена](cognitive-services-custom-subdomains.md) учетной записи Cognitive Services.
> * Запрос должен быть получен из разрешенного списка IP-адресов.
>
> Запросы от других служб Azure, в том числе портала Azure, служб метрики и ведения журналов, блокируются.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Сценарии

Чтобы защитить ресурс Cognitive Services, сначала необходимо настроить правило, запрещающее доступ к трафику из всех сетей (включая Интернет-трафик) по умолчанию. Затем следует настроить правила, предоставляющие доступ к трафику из конкретных виртуальных сетей. Такая конфигурация позволяет создать для приложений границу в виде безопасной сети. Кроме того, можно настроить правила для предоставления доступа к трафику из диапазона общедоступных IP-адресов в Интернете, что позволяет устанавливать подключения от конкретных клиентов в Интернете или локальных клиентах.

Сетевые правила применяются ко всем сетевым протоколам в Azure Cognitive Services, включая протоколы RESTFUL и WebSocket. Для доступа к данным с помощью таких средств, как тестовые консоли Azure, необходимо настроить явные правила сети. Правила сети можно применять к существующим Cognitive Services ресурсам или при создании новых ресурсов Cognitive Services. Как только правила сети применены, они распространяются на все запросы.

## <a name="supported-regions-and-service-offerings"></a>Поддерживаемые регионы и предложения услуг

Поддержка виртуальных сетей для Cognitive Services ограничена в регионах Azure *ранний-Central US (euap)* , *Западная Европа*и *Западная часть США 2* . Кроме того, не все предложения по обприятию услуг поддерживают виртуальные сети. Следующие предложения по обприятию службы позволяют использовать виртуальные сети. Если предложение услуги отсутствует в списке, оно не поддерживает виртуальные сети.

> [!div class="checklist"]
> * [Детектор аномалий](./anomaly-detector/index.yml)
> * [Компьютерное зрение](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Пользовательское визуальное распознавание](./custom-vision-service/index.yml)
> * [Распознавание лиц](./face/index.yml)
> * [Распознаватель форм](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Персонализация](./personalizer/index.yml)
> * [Анализ текста](./text-analytics/index.yml)

## <a name="change-the-default-network-access-rule"></a>Изменение сетевого правила доступа по умолчанию

По умолчанию Cognitive Services ресурсы принимают подключения от клиентов в любой сети. Для ограничения доступа из выбранных сетей необходимо сначала изменить действие по умолчанию.

> [!WARNING]
> Внесение изменений в правила сети может повлиять на возможность приложения подключаться к Azure Cognitive Services. Если присвоить сетевому правилу по умолчанию значение **Deny** , блокируются все доступ к данным, если также не применяются определенные правила сети, **предоставляющие** доступ. Предоставьте доступ для разрешенных сетей с помощью сетевых правил, прежде чем изменить правила по умолчанию и запретить доступ. Если вы разрешаете вывод IP-адресов для локальной сети, обязательно добавьте все возможные исходящие общедоступные IP-адреса из локальной сети.

### <a name="managing-default-network-access-rules"></a>Управление сетевыми правилами доступа по умолчанию

Вы можете управлять правилами сетевого доступа по умолчанию для Cognitive Services ресурсов с помощью портал Azure, PowerShell или Azure CLI.

# <a name="azure-portaltabportal"></a>[портал Azure](#tab/portal)

1. Перейдите к ресурсу Cognitive Services, который необходимо защитить.

1. Выберите меню " **Управление ресурсами** " — " **Виртуальная сеть**".

   ![Параметр виртуальной сети](media/vnet/virtual-network-blade.png)

1. Чтобы запретить доступ по умолчанию, разрешите доступ в разделе **Выбранные сети**. Если выбран параметр "только **Выбранные сети** ", несопровождаемые настроенными **виртуальными сетями** или **диапазонами адресов** — весь доступ фактически запрещается. Если доступ запрещен, запросы, пытающиеся использовать ресурс Cognitive Services, запрещены. Портал Azure, Azure PowerShell или Azure CLI можно по-прежнему использовать для настройки ресурса Cognitive Services.
1. Чтобы разрешить передачу трафика из всех сетей, разрешите доступ в разделе **Все сети**.

   ![Виртуальные сети запрещают](media/vnet/virtual-network-deny.png)

1. Щелкните **Сохранить**, чтобы применить изменения.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Установите [Azure PowerShell](/powershell/azure/install-az-ps) и [выполните вход](/powershell/azure/authenticate-azureps), или выберите **попробовать**.

1. Отображение состояния правила по умолчанию для ресурса Cognitive Services.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Настройте в правиле по умолчанию запрет сетевого доступа по умолчанию.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Настройте в правиле по умолчанию разрешение сетевого доступа по умолчанию.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli), или выберите **попробовать**.

1. Отображение состояния правила по умолчанию для ресурса Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Настройте в правиле по умолчанию запрет сетевого доступа по умолчанию.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Настройте в правиле по умолчанию разрешение сетевого доступа по умолчанию.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Предоставление доступа из виртуальной сети

Вы можете настроить Cognitive Services ресурсов, чтобы разрешить доступ только из конкретных подсетей. Разрешенные подсети могут принадлежать к виртуальной сети в одной подписке или в другой подписке, включая подписки, принадлежащие другому клиенту Azure Active Directory.

Включите [конечную точку службы](../virtual-network/virtual-network-service-endpoints-overview.md) для Cognitive Services Azure в виртуальной сети. Конечная точка службы направляет трафик из виртуальной сети в оптимальный путь к службе Cognitive Services Azure. Удостоверения подсети и виртуальной сети также передаются вместе с каждым запросом. Затем администраторы могут настроить сетевые правила для ресурса Cognitive Services, которые позволяют получать запросы из конкретных подсетей в виртуальной сети. Клиенты, которым предоставлен доступ через эти правила сети, должны продолжать отвечать требованиям к авторизации Cognitive Services ресурса для доступа к данным.

Каждый ресурс Cognitive Services поддерживает до 100 правил виртуальной сети, которые могут сочетаться с [правилами IP-сети](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Необходимые разрешения

Чтобы применить правило виртуальной сети к ресурсу Cognitive Services, пользователь должен иметь соответствующие разрешения для добавляемых подсетей. Требуемое разрешение является ролью *участника* по умолчанию или ролью *участника Cognitive Services* . Необходимые разрешения также можно добавить в определения пользовательских ролей.

Cognitive Services ресурс и виртуальные сети, которым предоставлен доступ, могут находиться в разных подписках, включая подписки, которые являются частью другого клиента Azure AD.

> [!NOTE]
> Конфигурация правил, предоставляющих доступ к подсетям в виртуальных сетях, которые являются частью другого клиента Azure Active Directory, в настоящее время поддерживаются только с помощью PowerShell, интерфейса командной строки и API-интерфейсов RESTFUL. Такие правила нельзя настроить с помощью портал Azure, хотя они могут быть просмотрены на портале.

### <a name="managing-virtual-network-rules"></a>Управление правилами виртуальной сети

Вы можете управлять правилами виртуальной сети для Cognitive Servicesных ресурсов с помощью портал Azure, PowerShell или Azure CLI.

# <a name="azure-portaltabportal"></a>[портал Azure](#tab/portal)

1. Перейдите к ресурсу Cognitive Services, который необходимо защитить.

1. Выберите меню " **Управление ресурсами** " — " **Виртуальная сеть**".

1. Убедитесь, что вы разрешили доступ в разделе **Выбранные сети**.

1. Чтобы предоставить доступ к виртуальной сети с существующим сетевым правилом, в разделе **виртуальные сети**выберите **Добавить существующую виртуальную сеть**.

   ![Добавить существующую виртуальную сеть](media/vnet/virtual-network-add-existing.png)

1. Выберите параметры **виртуальные сети** и **подсети** , а затем щелкните **включить**.

   ![Добавить сведения о существующей виртуальной сети](media/vnet/virtual-network-add-existing-details.png)

1. Чтобы создать новую виртуальную сеть и предоставить ей доступ, выберите **Добавить новую виртуальную сеть**.

   ![Добавить новую виртуальную сеть](media/vnet/virtual-network-add-new.png)

1. Укажите сведения, необходимые для создания новой виртуальной сети, а затем нажмите кнопку **создать**.

   ![Создать виртуальную сеть](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Если конечная точка службы Cognitive Services Azure ранее не была настроена для выбранной виртуальной сети и подсетей, ее можно настроить в рамках этой операции.
    >
    > В настоящее время для выбора при создании правила отображаются только виртуальные сети, принадлежащие одному и тому же клиенту Azure Active Directory. Чтобы предоставить доступ к подсети в виртуальной сети, принадлежащей другому клиенту, используйте PowerShell, интерфейс командной строки или API-интерфейсы RESTFUL.

1. Чтобы удалить правило виртуальной сети или подсети, выберите **...** , чтобы открыть контекстное меню для виртуальной сети или подсети, и выберите **Удалить**.

   ![Удалить виртуальную сеть](media/vnet/virtual-network-remove.png)

1. Щелкните **Сохранить**, чтобы применить изменения.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Установите [Azure PowerShell](/powershell/azure/install-az-ps) и [выполните вход](/powershell/azure/authenticate-azureps), или выберите **попробовать**.

1. Выведите список правил виртуальной сети.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Включите конечную точку службы для Cognitive Services Azure в существующей виртуальной сети и подсети.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Добавьте сетевое правило для виртуальной сети и подсети.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Чтобы добавить сетевое правило для подсети, принадлежащей другому клиенту Azure AD, используйте полный параметр **виртуалнетворкресаурцеид** в форме "/субскриптионс/субскриптион-ИД/ресаурцеграупс/ресаурцеграуп-наме/провидерс/Микрософт.Нетворк/виртуалнетворкс/внет-наме/субнетс/субнет-наме".

1. Удалите сетевое правило для виртуальной сети и подсети.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli), или выберите **попробовать**.

1. Выведите список правил виртуальной сети.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Включите конечную точку службы для Cognitive Services Azure в существующей виртуальной сети и подсети.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Добавьте сетевое правило для виртуальной сети и подсети.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Чтобы добавить правило для подсети, принадлежащей другому клиенту Azure AD, используйте полный идентификатор подсети в формате "/Субскриптионс/субскриптион-ИД/ресаурцеграупс/ресаурцеграуп-наме/провидерс/Микрософт.Нетворк/виртуалнетворкс/внет-наме/субнетс/субнет-наме".
    > 
    > Чтобы получить идентификатор подсети для виртуальной сети, принадлежащей другому клиенту Azure AD, можно использовать параметр **Subscription** .

1. Удалите сетевое правило для виртуальной сети и подсети.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Обязательно [укажите для правила по умолчанию](#change-the-default-network-access-rule) значение **deny**, иначе сетевые правила не будут действовать.

## <a name="grant-access-from-an-internet-ip-range"></a>Предоставление доступа из диапазона IP-адресов в Интернете

Можно настроить ресурсы Cognitive Services, чтобы разрешить доступ из конкретных диапазонов общедоступных IP-адресов Интернета. Эта конфигурация предоставляет доступ к конкретным службам и локальным сетям, что позволяет эффективно блокировать общий Интернет-трафик.

Укажите разрешенные диапазоны адресов Интернета, используя [нотацию CIDR](https://tools.ietf.org/html/rfc4632) в форме `16.17.18.0/24` или как отдельные IP-адреса, такие как `16.17.18.19`.

   > [!Tip]
   > Небольшие адреса, использующие размеры с префиксом /31 или /32, не поддерживаются. Эти диапазоны следует настраивать с помощью отдельных правил для IP-адресов.

Правила IP-сети можно применять только для **общедоступных** IP-адресов в Интернете. Диапазоны IP-адресов, зарезервированные для частных сетей (как определено в документе [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)), запрещено использовать в правилах IP. Частные сети включают адреса, которые начинаются с `10.*`, `172.16.*` - `172.31.*`и `192.168.*`.

   > [!NOTE]
   > Правила IP-сети не влияют на запросы, исходящие из того же региона Azure, что и ресурс Cognitive Services. Используйте [правила виртуальной сети](#grant-access-from-a-virtual-network) для разрешения запросов из того же региона.

Сейчас поддерживаются только IPV4-адреса. Каждый ресурс Cognitive Services поддерживает до 100 правил сети IP, которые могут сочетаться с [правилами виртуальной сети](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Настройка доступа из локальных сетей

Чтобы предоставить доступ к ресурсу Cognitive Services из локальных сетей с помощью правила IP-сети, необходимо найти IP-адреса, используемые сетью в Интернете. За помощью обращайтесь к администратору сети.

Если вы используете [ExpressRoute](../expressroute/expressroute-introduction.md) в локальной среде для общедоступного пиринга или пиринга Майкрософт, вам необходимо будет выяснить IP-адреса NAT. Для общедоступного пиринга каждый канал ExpressRoute по умолчанию использует два IP-адреса NAT. Каждый из них применяется к трафику службы Azure, когда трафик входит в Microsoft Azureную магистральную сеть. Для пиринга Майкрософт используются IP-адреса NAT либо предоставленные клиентом, либо предоставленные поставщиком услуг. Чтобы разрешить доступ к ресурсам служб, необходимо разрешить эти общедоступные IP-адреса в настройках брандмауэра IP-адресов ресурсов. Чтобы найти IP-адреса канала ExpressRoute для общедоступного пиринга, [отправьте запрос по ExpressRoute в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) через портал Azure. Узнайте больше о [NAT для общедоступного пиринга и пиринга Майкрософт](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>Управление правилами IP-сети

Вы можете управлять правилами IP-сети для Cognitive Servicesных ресурсов с помощью портал Azure, PowerShell или Azure CLI.

# <a name="azure-portaltabportal"></a>[портал Azure](#tab/portal)

1. Перейдите к ресурсу Cognitive Services, который необходимо защитить.

1. Выберите меню " **Управление ресурсами** " — " **Виртуальная сеть**".

1. Убедитесь, что вы разрешили доступ в разделе **Выбранные сети**.

1. Чтобы предоставить доступ к диапазону IP-адресов в Интернете, введите IP-адрес или диапазон адресов (в [формате CIDR](https://tools.ietf.org/html/rfc4632)) в разделе **брандмауэр** > **адрес**. Принимаются только допустимые общедоступные IP-адреса (не зарезервированные).

   ![Добавить диапазон IP-адресов](media/vnet/virtual-network-add-ip-range.png)

1. Чтобы удалить правило IP-сети, щелкните <span class="docon docon-delete x-hidden-focus"></span> значок корзины рядом с диапазоном адресов.

   ![Удалить диапазон IP-адресов](media/vnet/virtual-network-delete-ip-range.png)

1. Щелкните **Сохранить**, чтобы применить изменения.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Установите [Azure PowerShell](/powershell/azure/install-az-ps) и [выполните вход](/powershell/azure/authenticate-azureps), или выберите **попробовать**.

1. Выведите список правил IP-сети.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Добавьте правило сети для отдельного IP-адреса.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Добавьте правило сети для диапазона IP-адресов.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Удалите правило сети для отдельного IP-адреса.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Удалите правило сети для диапазона IP-адресов.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [выполните вход](/cli/azure/authenticate-azure-cli), или выберите **попробовать**.

1. Выведите список правил IP-сети.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Добавьте правило сети для отдельного IP-адреса.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Добавьте правило сети для диапазона IP-адресов.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Удалите правило сети для отдельного IP-адреса.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Удалите правило сети для диапазона IP-адресов.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Обязательно [укажите для правила по умолчанию](#change-the-default-network-access-rule) значение **deny**, иначе сетевые правила не будут действовать.

## <a name="next-steps"></a>Дальнейшие действия

* Изучите различные [Cognitive Services Azure](welcome.md)
* Дополнительные сведения о [конечных точках службы виртуальной сети Azure](../virtual-network/virtual-network-service-endpoints-overview.md)