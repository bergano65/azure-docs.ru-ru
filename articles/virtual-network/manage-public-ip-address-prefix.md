---
title: Создание, изменение или удаление префикса общедоступного IP-адреса Azure
titlesuffix: Azure Virtual Network
description: Сведения о создании, изменении и удалении префикса общедоступного IP-адреса.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: anavin
ms.openlocfilehash: 5f0c2d9757f3652b0f83b8c36d89c855f7a92fdd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383872"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Создание, изменение и удаление префикса общедоступного IP-адреса

Общие сведения о префиксе общедоступного IP-адреса, а также о его создании, изменении и удалении. Префикс общедоступного IP-адреса — это непрерывный диапазон адресов на основе указанного количества общедоступных IP-адресов. Адреса назначаются для вашей подписки. При создании общедоступного ресурса IP-адреса можно назначить статический общедоступный IP-адрес из префикса и связать адрес с виртуальными машинами, балансизаторами нагрузки или другими ресурсами, чтобы обеспечить подключение к Интернету. Если вы не знакомы с префиксами общедоступных IP-адресов, см. раздел [Обзор префикс общедоступного IP-адреса](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Перед началом

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Перед выполнением действий, описанных в любом разделе этой статьи, выполните следующие задачи.

- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной учетной записи](https://azure.microsoft.com/free).
- При использовании портала перейдите по адресу https://portal.azure.com и войдите с использованием своей учетной записи Azure.
- При использовании команд PowerShell для работы с этой статьей выполняйте их в [Azure Cloud Shell](https://shell.azure.com/powershell) или в PowerShell на своем компьютере. Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Для работы с этим руководством требуется модуль Azure PowerShell версии 1.0.0 и выше. Выполните командлет `Get-Module -ListAvailable Az`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.
- При использовании команд интерфейса командной строки Azure (CLI) для работы с этой статьей выполняйте их в [Azure Cloud Shell](https://shell.azure.com/bash) или в интерфейсе командной строки на своем компьютере. Для этого руководства требуется Azure CLI 2.0.41 или более поздней версии. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). Если Azure CLI запущен локально, необходимо также выполнить командлет `az login`, чтобы создать подключение к Azure.

Учетная запись, в которую вы входите или подключаетесь к Azure, должна быть назначена роли [сетевого вкладчика](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательской роли,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) которая присваивается соответствующим действиям, перечисленным в [Permissions.](#permissions)

Префиксы общедоступных IP-адресов предоставляются за дополнительную плату. Подробные сведения см. в разделе [Цены](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Создание префикса общедоступного IP-адреса

1. Выберите **+ Создать ресурс** в левом верхнем углу портала.
2. Введите *общедоступный ip-префикс* в поле *Поиска Marketplace.* Когда в результатах поиска появится **Префикс общедоступного IP-адреса**, щелкните его.
3. В колонке **Префикс общедоступного IP-адреса** щелкните **Создать**.
4. Введите или выберите значения для перечисленных ниже параметров в разделе **Создать префикс общедоступного IP-адреса**, а затем щелкните **Создать**.

   |Параметр|Обязательно?|Сведения|
   |---|---|---|
   |Подписка|Да|Ресурс в пределах одной и той же [подписки](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), с которым будет связан общедоступный IP-адрес.|
   |Группа ресурсов|Да|Ресурс в пределах одной или разных [групп ресурсов](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), с которым будет связан общедоступный IP-адрес.|
   |Имя|Да|Имя должно быть уникальным в пределах выбранной группы ресурсов.|
   |Регион|Да|Должен существовать в том же [регионе](https://azure.microsoft.com/regions), что и общедоступные IP-адреса, которым вы будете назначать адреса из диапазона.|
   |Размер префикса|Да| Нужный размер префикса. По умолчанию это /28 или 16 IP-адресов.

**Команды**

|Средство|Get-Help|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Создание статического общедоступного IP-адреса из префикса
После создания префикса необходимо создать статический IP-адрес из префикса. Для этого сделайте следующее.

1. В верхней части портала Azure в поле с текстом *Поиск ресурсов* введите *префикс общедоступного IP-адреса*. Когда в результатах поиска появится **Префикс общедоступного IP-адреса**, щелкните его.
2. Выберите префикс, из которого вы будете создавать общедоступные IP-адреса.
3. Когда он появится в результатах поиска, выберите его и щелкните **+Добавить IP-адрес** в разделе "Обзор".
4. Введите или выберите значения для перечисленных ниже параметров в разделе **Создать общедоступный IP-адрес**. Так как префикс предназначен для номера SKU ценовой категории "Стандартный" и статического адреса IPv4, необходимо предоставить следующие сведения:

   |Параметр|Обязательно?|Сведения|
   |---|---|---|
   |Имя|Да|Имя общедоступного IP-адреса должно быть уникальным в пределах выбранной группы ресурсов.|
   |Время ожидания простоя (в минутах)|нет|Время (в минутах), в течение которого подключение TCP или HTTP остается открытым без привязки к клиентам при отправке запросов для проверки активности. |
   |Метка имени DNS|нет|Должна быть уникальной в пределах региона Azure, в котором создается имя (для всех подписок и клиентов). Azure автоматически регистрирует имя и IP-адрес в своей DNS (вы можете подключаться к ресурсу, используя это имя). Azure добавляет подсеть по умолчанию, например *location.cloudapp.azure.com* (где location — это выбранное вами расположение), к имени, которое вы предоставили, для создания полного имени DNS. Дополнительные сведения см. в разделе [Использование Azure DNS с общедоступным IP-адресом Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Кроме того, для создания ресурса public IP-адресовой версии (CLI) и PublicIpPrefix (PS) можно использовать команды CLI и PS. 

|Средство|Get-Help|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Просмотр или удаление префикса

1. В верхней части портала Azure в поле с текстом *Поиск ресурсов* введите *префикс общедоступного IP-адреса*. Когда в результатах поиска появится **Префикс общедоступного IP-адреса**, щелкните его.
2. Выберите из списка имя префикса общедоступного IP-адреса для просмотра, изменения параметров или удаления.
3. Выполните одно из следующих действий (в зависимости от того, нужно ли просмотреть, удалить или изменить этот префикс общедоступного IP-адреса).
   - **Просмотр**. В разделе **Обзор** отображаются основные параметры префикса общедоступного IP-адреса, такие как префикс.
   - **Удаление**. Чтобы удалить префикс общедоступного IP-адреса, в разделе **Обзор** щелкните **Удалить**. Если адреса в пределах префикса связаны с ресурсами общедоступных IP-адресов, необходимо сначала удалить ресурсы общедоступных IP-адресов. См. раздел [Удаление общедоступного IP-адреса](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Команды**

|Средство|Get-Help|
|---|---|
|CLI|Команда [az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) выводит список общедоступных IP-адресов, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) — отображает параметры, [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) — обновляет, а [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) — удаляет|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) для получения общедоступного объекта IP-адреса и просмотра его настроек, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) для обновления настроек; [Удалить-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) для удаления|

## <a name="permissions"></a>Разрешения

Для выполнения задач с префиксами общедоступных IP-адресов учетной записи должна быть назначена роль [Участник сетей](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательская](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) роль, которой назначены соответствующие разрешения, перечисленные в таблице ниже.

| Действие                                                            | Имя                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Чтение префикса общедоступного IP-адреса                                |
| Microsoft.Network/publicIPPrefixes/write                          | Создание или обновление префикса общедоступного IP-адреса                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Удаление префикса общедоступного IP-адреса                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Создание общедоступного IP-адреса из префикса |

## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о сценариях и преимуществах использования [префикса общедоступного IP-адреса](public-ip-address-prefix.md)
