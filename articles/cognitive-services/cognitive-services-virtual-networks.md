---
title: Виртуальные сети
titleSuffix: Azure Cognitive Services
description: Налаживайте многоуровневую сетевую безопасность для ресурсов Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371228"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Настройка виртуальных сетей Azure Cognitive Services

Azure Cognitive Services предоставляет многоуровневую модель безопасности. Эта модель позволяет обезопасить учетные записи Когнитивных Служб в определенном подмножестве сетей. При настройке сетевых правил доступ к учетной записи могут получить только приложения, запрашивающие данные по указанному набору сетей. Можно ограничить доступ к ресурсам с помощью фильтрации запросов. Разрешение только запросов, происходящих с указанных IP-адресов, IP-диапазонов или из списка подсетей в [виртуальных сетях Azure.](../virtual-network/virtual-networks-overview.md) Если вы заинтересованы в этом предложении, вам нужно [будет запросить предварительный доступ.](https://aka.ms/cog-svc-vnet-signup)

Приложение, которое получает доступ к ресурсу Cognitive Services, когда сетевые правила действуют, требует авторизации. Авторизация поддерживается учетными данными [Active Directory (Azure](../active-directory/fundamentals/active-directory-whatis.md) AD) или с действительным ключом API.

> [!IMPORTANT]
> Включение правил брандмауэра для учетной записи Cognitive Services блокирует входящие запросы данных по умолчанию. Для того, чтобы разрешить запросы, необходимо выполнить одно из следующих условий:
> * Запрос должен исходить от службы, работающей в виртуальной сети Azure (VNet) в разрешенном списке подсети целевой учетной записи Когнитивных Услуг. Конечная точка запросов, исходящих от VNet, должна быть установлена в качестве [пользовательского поддомена](cognitive-services-custom-subdomains.md) вашей учетной записи Когнитивных Услуг.
> * Или запрос должен исходить из разрешенного списка IP-адресов.
>
> Запросы от других служб Azure, в том числе портала Azure, служб метрики и ведения журналов, блокируются.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Сценарии

Чтобы обезопасить ресурс Cognitive Services, необходимо сначала настроить правило, чтобы отказать в доступе к трафику из всех сетей (включая интернет-трафик) по умолчанию. Затем следует настроить правила, которые предоставляют доступ к трафику из определенных VNets. Такая конфигурация позволяет создать для приложений границу в виде безопасной сети. Вы также можете настроить правила, чтобы предоставить доступ к трафику из некоторых общедоступных диапазонов IP-адресов интернета, что позволяет подключение от конкретных интернет или на местах клиентов.

Сетевые правила применяются во всех сетевых протоколах для когнитивных служб Azure, включая REST и WebSocket. Для доступа к данным с помощью таких инструментов, как тестовые консоли Azure, необходимо настроить явные сетевые правила. Можно применять сетевые правила к существующим ресурсам Cognitive Services или при создании новых ресурсов Cognitive Services. Как только правила сети применены, они распространяются на все запросы.

## <a name="supported-regions-and-service-offerings"></a>Поддерживаемые регионы и предложения услуг

Виртуальная сетевая поддержка когнитивных услуг, перечисленных ниже, ограничена *Центральным ИСЕАП США*, *южным центральным США,* *Восточной ЧАСТЬю США,* *Западной Америкой 2,* *Северной Европой,* *Северной Африкой, Северной* *ИСеверной Европой,* *Центральной Индией,* *Австралией На востоке,* *Западной АМЕРИКАНСКОй*областью, и регионами *США Gov Virginia* Azure. Если предложение сервиса не перечислено здесь, оно не поддерживает виртуальные сети.

> [!div class="checklist"]
> * [Детектор аномалий](./anomaly-detector/index.yml)
> * [API Компьютерного зрения](./computer-vision/index.yml)
> * [Модератор контента](./content-moderator/index.yml)
> * [Пользовательское видение](./custom-vision-service/index.yml)
> * [Распознавание лиц](./face/index.yml)
> * [Распознаватель документов](./form-recognizer/index.yml)
> * [Луис](./luis/index.yml)
> * [Персонализатор](./personalizer/index.yml)
> * [Текстовая аналитика](./text-analytics/index.yml)
> * [Зна Чайник](./qnamaker/index.yml)

Виртуальная сетевая поддержка cognitive Services, перечисленных ниже, ограничена *регионами Центрального Европейского АСВ*США, *южной части Центральной части США,* *Восточной Америки,* *Западной АМЕРИКАНСКОй, 2,* *Глобальной,* и *регионами GOV Virginia* Azure.
> [!div class="checklist"]
> * [Текст переводчика](./translator/index.yml)

## <a name="service-tags"></a>Теги служб
В дополнение к поддержке конечных точек виртуального сетевого обслуживания для вышеуказанных служб, Cognitive Services также поддерживает тег обслуживания для конфигурации исходящих сетевых правил. Следующие услуги включены в тег службы CognitiveServicesManagement.
> [!div class="checklist"]
> * [Детектор аномалий](./anomaly-detector/index.yml)
> * [API Компьютерного зрения](./computer-vision/index.yml)
> * [Модератор контента](./content-moderator/index.yml)
> * [Пользовательское видение](./custom-vision-service/index.yml)
> * [Распознавание лиц](./face/index.yml)
> * [Распознаватель документов](./form-recognizer/index.yml)
> * [Луис](./luis/index.yml)
> * [Персонализатор](./personalizer/index.yml)
> * [Текстовая аналитика](./text-analytics/index.yml)
> * [Зна Чайник](./qnamaker/index.yml)
> * [Текст переводчика](./translator/index.yml)
> * [Служба "Речь"](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Изменение сетевого правила доступа по умолчанию

По умолчанию ресурсы Cognitive Services принимают соединения от клиентов в любой сети. Для ограничения доступа из выбранных сетей необходимо сначала изменить действие по умолчанию.

> [!WARNING]
> Внесение изменений в сетевые правила может повлиять на способность приложений подключаться к когнитивным службам Azure. Установка правила сети по умолчанию, чтобы **запретить** блокировать доступ к данным, если не применяются конкретные сетевые правила, которые **предоставляют** доступ. Предоставьте доступ для разрешенных сетей с помощью сетевых правил, прежде чем изменить правила по умолчанию и запретить доступ. Если вы разрешаете перечислять IP-адреса для вашей сети, обязательно добавляйте все возможные исходящие общедоступные IP-адреса из вашей предварительной сети.

### <a name="managing-default-network-access-rules"></a>Управление сетевыми правилами доступа по умолчанию

Вы можете управлять правилами доступа к сети по умолчанию для ресурсов Cognitive Services через портал Azure, PowerShell или Azure CLI.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

1. Перейдите на ресурс Cognitive Services, который вы хотите защитить.

1. Выберите меню **RESOURCE MANAGEMENT** под названием **Виртуальная сеть.**

   ![Вариант виртуальной сети](media/vnet/virtual-network-blade.png)

1. Чтобы отказать в доступе по умолчанию, выберите разрешить доступ из **выбранных сетей.** При настройке **отдельных сетей,** несопровождаемых настроенными **виртуальными сетями** или **диапазонами адресов-** весь доступ фактически отказано. При отказе в доступе запросы, пытающиеся использовать ресурс Cognitive Services, не допускаются. Портал Azure, Azure PowerShell или Azure CLI по-прежнему можно использовать для настройки ресурса Cognitive Services.
1. Чтобы разрешить трафик из всех сетей, выберите разрешить доступ из **всех сетей.**

   ![Виртуальные сети отрицают](media/vnet/virtual-network-deny.png)

1. Щелкните **Сохранить**, чтобы применить изменения.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Установите [Azure PowerShell](/powershell/azure/install-az-ps) и [восвайтесь](/powershell/azure/authenticate-azureps)или выберите **Попробуйте.**

1. Отобразить состояние правила по умолчанию для ресурса Cognitive Services.

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

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [вопийте](/cli/azure/authenticate-azure-cli)в систему или выберите **Попробуйте его.**

1. Отобразить состояние правила по умолчанию для ресурса Cognitive Services.

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

Вы можете настроить ресурсы Cognitive Services, чтобы обеспечить доступ только из определенных подсетей. Разрешенные подсети могут принадлежать VNet в той же подписке или в другой подписке, включая подписки, принадлежащие другому арендатору Active Directory Azure.

Включить [конечную точку службы](../virtual-network/virtual-network-service-endpoints-overview.md) для когнитивных служб Azure в VNet. Конечная точка службы направляет трафик из VNet по оптимальному пути к службе когнитивных услуг Azure. Идентификационные данные подсети и виртуальной сети также передаются с каждым запросом. Администраторы могут настроить сетевые правила для ресурса Cognitive Services, которые позволяют получать запросы из определенных подсетей в VNet. Клиенты, получивдоступы через эти сетевые правила, должны продолжать удовлетворять требованиям авторизации ресурса Cognitive Services для доступа к данным.

Каждый ресурс Cognitive Services поддерживает до 100 виртуальных сетевых правил, которые могут быть объединены с [правилами IP-сети.](#grant-access-from-an-internet-ip-range)

### <a name="required-permissions"></a>Необходимые разрешения

Чтобы применить виртуальное сетевое правило к ресурсу Cognitive Services, пользователь должен иметь соответствующие разрешения на добавление подсетей. Требуемое разрешение — это роль *вкладчика* по умолчанию или роль *вкладчика когнитивных служб.* Необходимые разрешения также могут быть добавлены к пользовательским определениям ролей.

Ресурс Cognitive Services и виртуальные сети, получивших доступ, могут быть в различных подписках, включая подписки, которые являются частью другого арендатора Azure AD.

> [!NOTE]
> Конфигурация правил, которые предоставляют доступ к подсетям в виртуальных сетях, которые являются частью другого арендатора Active Directory Azure, в настоящее время поддерживается только через Powershell, CLI и REST AI. Такие правила не могут быть настроены через портал Azure, хотя их можно просматривать на портале.

### <a name="managing-virtual-network-rules"></a>Управление правилами виртуальной сети

Управлять виртуальными сетевыми правилами для ресурсов Cognitive Services можно через портал Azure, PowerShell или ClI Azure.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

1. Перейдите на ресурс Cognitive Services, который вы хотите защитить.

1. Выберите меню **RESOURCE MANAGEMENT** под названием **Виртуальная сеть.**

1. Убедитесь, что вы разрешили доступ в разделе **Выбранные сети**.

1. Чтобы предоставить доступ к виртуальной сети с существующим правилом сети, под **виртуальными сетями,** выберите **Добавить существующую виртуальную сеть.**

   ![Добавление существующего vNet](media/vnet/virtual-network-add-existing.png)

1. Выберите **параметры виртуальных сетей** и **подсетей,** а затем выберите **Включить.**

   ![Добавление существующих деталей vNet](media/vnet/virtual-network-add-existing-details.png)

1. Чтобы создать новую виртуальную сеть и предоставить ей доступ, выберите **Добавить новую виртуальную сеть.**

   ![Добавить новый vNet](media/vnet/virtual-network-add-new.png)

1. Предоставьте информацию, необходимую для создания новой виртуальной сети, а затем выберите **Create.**

   ![Создание vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Если конечная точка службы для Azure Cognitive Services ранее не была настроена для выбранной виртуальной сети и подсетей, ее можно настроить как часть этой операции.
    >
    > В настоящее время для выбора при создании правил отображаются только виртуальные сети, принадлежащие к тому же арендатору Active Directory Azure. Для предоставления доступа к подсети в виртуальной сети, принадлежащей другому арендатору, пожалуйста, используйте Powershell, CLI или REST AIS.

1. Чтобы удалить правило виртуальной сети или подсети, выберите **...,** чтобы открыть контекстное меню для виртуальной сети или подсети, и выберите **Удалить**.

   ![Удалить vNet](media/vnet/virtual-network-remove.png)

1. Щелкните **Сохранить**, чтобы применить изменения.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Установите [Azure PowerShell](/powershell/azure/install-az-ps) и [восвайтесь](/powershell/azure/authenticate-azureps)или выберите **Попробуйте.**

1. Выведите список правил виртуальной сети.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Включить конечную точку обслуживания для когнитивных служб Azure в существующей виртуальной сети и подсети.

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
    > Чтобы добавить сетевое правило для подсети в VNet, принадлежащем другому арендатору Azure AD, используйте полностью квалифицированный параметр **VirtualNetworkResourceId** в форме "/подписка/подписка-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

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

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [вопийте](/cli/azure/authenticate-azure-cli)в систему или выберите **Попробуйте его.**

1. Выведите список правил виртуальной сети.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Включить конечную точку обслуживания для когнитивных служб Azure в существующей виртуальной сети и подсети.

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
    > Чтобы добавить правило для подсети в VNet, принадлежащее другому арендатору Azure AD, используйте полностью квалифицированный идентификатор подсети в форме "/подписка/подписка-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Можно использовать параметр **подписки** для получения идентификатора подсети для VNet, принадлежащего другому арендатору Azure AD.

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

Вы можете настроить ресурсы Cognitive Services, чтобы обеспечить доступ из определенных общедоступных диапазонов IP-адресов. Эта конфигурация предоставляет доступ к конкретным службам и наместным сетям, эффективно блокируя общий интернет-трафик.

Предоставить разрешенные диапазоны интернет-адресов `16.17.18.0/24` с использованием [обозначения CIDR](https://tools.ietf.org/html/rfc4632) в форме или в качестве отдельных IP-адресов, как `16.17.18.19`.

   > [!Tip]
   > Небольшие адреса, использующие размеры с префиксом /31 или /32, не поддерживаются. Эти диапазоны следует настраивать с помощью отдельных правил для IP-адресов.

Правила IP-сети можно применять только для **общедоступных** IP-адресов в Интернете. Диапазоны IP-адресов, зарезервированные для частных сетей (как определено в документе [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)), запрещено использовать в правилах IP. Частные сети включают `10.*`адреса, которые начинаются с `172.16.*`  -  `172.31.*`, и `192.168.*`.

   > [!NOTE]
   > Правила IP-сети не влияют на запросы, поступающие из того же региона Azure, что и ресурс Cognitive Services. Используйте [правила виртуальной сети](#grant-access-from-a-virtual-network) для разрешения запросов в одном и том же регионе.

Сейчас поддерживаются только IPV4-адреса. Каждый ресурс Cognitive Services поддерживает до 100 правил IP-сети, которые могут быть объединены с [правилами виртуальной сети.](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Настройка доступа из локальных сетей

Чтобы предоставить доступ из ваших сетей к ресурсу Cognitive Services с помощью правила IP-сети, необходимо определить IP-адреса, используемые вашей сетью. За помощью обращайтесь к администратору сети.

Если вы используете [ExpressRoute](../expressroute/expressroute-introduction.md) на месте для публичного пиринга или Microsoft пиринг, вам нужно будет определить IP-адреса NAT. Для публичного пиринга каждая схема ExpressRoute по умолчанию использует два IP-адреса NAT. Каждый из них применяется к трафику службы Azure при входе трафика в основную часть сети Microsoft Azure. Для пиринга корпорации Майкрософт используемые IP-адреса NAT либо предоставляются заказчиком, либо предоставляются поставщиком услуг. Чтобы разрешить доступ к ресурсам служб, необходимо разрешить эти общедоступные IP-адреса в настройках брандмауэра IP-адресов ресурсов. Чтобы найти IP-адреса канала ExpressRoute для общедоступного пиринга, [отправьте запрос по ExpressRoute в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) через портал Azure. Узнайте больше о [NAT для общедоступного пиринга и пиринга Майкрософт](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>Управление правилами IP-сети

Вы можете управлять правилами IP-сети для ресурсов Когнитивных Служб через портал Azure, PowerShell или Azure CLI.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

1. Перейдите на ресурс Cognitive Services, который вы хотите защитить.

1. Выберите меню **RESOURCE MANAGEMENT** под названием **Виртуальная сеть.**

1. Убедитесь, что вы разрешили доступ в разделе **Выбранные сети**.

1. Чтобы предоставить доступ к интернет-диапазону IP, введите IP-адрес или диапазон адресов (в [формате CIDR)](https://tools.ietf.org/html/rfc4632)под**диапазоном адресов** **Firewall.** >  Принимаются только действительные общедоступные IP-адреса (не зарезервированные) адреса.

   ![Добавление диапазона IP](media/vnet/virtual-network-add-ip-range.png)

1. Чтобы удалить правило IP-сети, <span class="docon docon-delete x-hidden-focus"></span> выберите значок канистра с мусором рядом с адресным диапазоном.

   ![Удаление диапазона IP](media/vnet/virtual-network-delete-ip-range.png)

1. Щелкните **Сохранить**, чтобы применить изменения.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Установите [Azure PowerShell](/powershell/azure/install-az-ps) и [восвайтесь](/powershell/azure/authenticate-azureps)или выберите **Попробуйте.**

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

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

1. Установите [Azure CLI](/cli/azure/install-azure-cli) и [вопийте](/cli/azure/authenticate-azure-cli)в систему или выберите **Попробуйте его.**

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

* Изучите различные [когнитивные службы Azure](welcome.md)
* Узнайте больше о [конечных точках обслуживания виртуальной сети Azure](../virtual-network/virtual-network-service-endpoints-overview.md)