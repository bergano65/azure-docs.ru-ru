---
title: С помощью PowerShell управления Azure службы Блокчейн-консорциума
description: Как управление участниками консорциума Блокчейн службы Azure с помощью PowerShell
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: f15fa3b4972a2ac54d1d9bce916fdd42c2951d2f
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550883"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Управление участниками консорциума Блокчейн службы Azure, с помощью PowerShell

Можно использовать PowerShell позволяют управлять участниками консорциума блокчейн, Блокчейн службы Azure. Члены с правами администратора может пригласить, добавить, удалить и изменения роли для всех участников консорциума блокчейна. Члены с привилегиями пользователя можно просмотреть все участники блокчейн-консорциума и можно изменить отображаемому имени члена.

## <a name="prerequisites"></a>Технические условия

* [Создание элемента блокчейн с помощью портала Azure](create-member.md)
* Дополнительные сведения о консорциумы, члены и узлов, см. в разделе [службе Блокчейн-консорциума](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

## <a name="install-powershell-module"></a>Установка модуля PowerShell

Установите пакет Microsoft.AzureBlockchainService.ConsortiumManagement.PS из коллекции PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-information-preference"></a>Набор сведений предпочтений

Дополнительные сведения можно получить при выполнении командлетов посредством установки сведения о привилегированной переменной. По умолчанию *$InformationPreference* присваивается *SilentlyContinue*.

Более подробные сведения из командлетов задания предпочтения в PowerShell следующим образом:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Установить соединение Web3

Позволяют управлять участниками консорциума, необходимо установить подключение к конечной точке члена службы Azure Blockchain Web3. Чтобы задать глобальные переменные, которые могут использоваться при вызове командлетов управления консорциума можно использовать этот скрипт.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Замените \<пароль учетной записи члена\> паролем учетной записи члена, которые использовались при создании элемента.

Найти другие значения на портале Azure:

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите к службе Azure Blockchain член по умолчанию **Обзор** страницы.

    ![Обзор членов](./media/manage-consortium-powershell/member-overview.png)

    Замените \<владелец учетной записи\>, и \<RootContract адрес\> со значениями с портала.

1. Адрес конечной точки, выберите **узлы транзакции** и выберите **по умолчанию** узла транзакций. Узел транзакций по умолчанию имеет имя, совпадающее с именем члена блокчейна.
1. Выберите элемент **Строки подключения**.

    ![Строки подключения](./media/manage-consortium-powershell/connection-strings.png)

    Замените \<адрес конечной точки\> со значением из **HTTPS (ключ доступа 1)** или **HTTPS (ключ доступа 2)**.

## <a name="network-and-smart-contract-management"></a>Сети и управления смарт-контрактов

Сети и смарт-контрактов командлеты можно используйте для подключения к вашей блокчейн конечной точки смарт-контрактов отвечают за управление консорциума.

### <a name="import-consortiummanagementcontracts"></a>Импорт ConsortiumManagementContracts

Подключается к консорциума управления смарт-контрактов, которые используются для элементов в пределах консорциума реализовать и управлять им.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| RootContractAddress | Параметр корневого адреса контракта, консорциума управления смарт-контрактов | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

**Пример**

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Импорт Web3Account

Чтобы создать объект, содержащий сведения учетной записи управления удаленным узлом с помощью этого командлета.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Адрес учетной записи участника Блокчейна | Да |
| ManagedAccountPassword | Пароль учетной записи адреса | Да |

**Пример**

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Новый Web3Connection

Устанавливает подключение к конечной точке RPC узла транзакции.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Адрес конечной точки участника Блокчейна | Да |

**Пример**

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Управление участниками консорциума

Для управления элементов в пределах консорциума используйте командлеты управления консорциума член. Доступные действия зависят от вашей роли консорциума.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Получает сведения об участнике или отображать список членов консорциума.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя члена службы Блокчейн в Azure, вы хотите получить сведения о на. Если имя элемента указано, возвращаются сведения элемента. Если имя указано, возвращается список всех членов консорциума. | Нет |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

**Пример**

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Пример выходных данных**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Удаляет член блокчейна.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя элемента для удаления | Да |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

**Пример**

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>SET-BlockchainMember

Задает атрибуты элементов, включая отображаемое имя и консорциум роль блокчейна.

Консорциум администраторы могут устанавливать **DisplayName** и **роли** для всех элементов. Консорциум члена роли пользователя, можно изменять только свои собственные член отображаемое имя.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя члена блокчейна | Да |
| DisplayName | Новое отображаемое имя | Нет |
| AccountAddress | Адрес учетной записи | Нет |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client |  Объект Web3Client, полученный из Нью-Web3Connection| Да |

**Пример**

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Управление приглашение участниками консорциума

Используйте консорциума член приглашение управления командлеты для управления консорциума член приглашения. Доступные действия зависят от вашей роли консорциума.

### <a name="new-blockchainmemberinvitation"></a>Новый BlockchainMemberInvitation

Пригласите новые элементы к консорциума.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure приглашенного участника | Да |
| Роль | Консорциум роль. Значения могут быть АДМИНИСТРАТОРОМ или ПОЛЬЗОВАТЕЛЕМ. АДМИНИСТРАТОР — это роль администратора консорциума. ПОЛЬЗОВАТЕЛЬ является консорциума члена роли. | Да |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

**Пример**

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Получает или перечисляет состояние приглашения консорциума элемента.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure приглашенного участника. Если идентификатор подписки не указан, возвращаются сведения о приглашении идентификатора подписки. Если идентификатор подписки указан, возвращается список всех приглашений члена. | Нет |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

**Пример**

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Пример выходных данных**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Отменяет приглашение консорциума члена.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure члена для отзыва | Да |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

**Пример**

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Наборы **роли** для существующих приглашения. Только консорциума могут быть изменены приглашения.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure приглашенного участника | Да |
| Роль | Новая роль консорциума приглашения. Значения могут быть **пользователя** или **АДМИНИСТРАТОРА** | Да |
| Участники |  Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

**Пример**

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о консорциумы, члены и узлов см. в разделе:

> [!div class="nextstepaction"]
> [Azure Service Блокчейн-консорциума](consortium.md)