---
title: С помощью PowerShell управления Azure службы Блокчейн-консорциума
description: Как управление участниками консорциума Блокчейн службы Azure с помощью PowerShell
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bef0c5d776e8ab6424b8604a49782088c45b0538
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028235"
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

```powershell
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="establish-a-web3-connection"></a>Установить соединение Web3

Позволяют управлять участниками консорциума, необходимо установить подключение к конечной точке члена службы Azure Blockchain Web3. Чтобы задать глобальные переменные, которые могут использоваться при вызове командлетов управления консорциума можно использовать этот скрипт.

```powershell
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

1. Адрес конечной точки, выберите **узлы транзакции** и выберите узел, транзакции.
1. Выберите **строки подключения**.

    ![Строки подключения](./media/manage-consortium-powershell/connection-strings.png)

    Замените \<адрес конечной точки\> со значением из **HTTPS (ключ доступа 1)** или **HTTPS (ключ доступа 2)**.

## <a name="network-and-smart-contract-management"></a>Сети и управления смарт-контрактов

Сети и смарт-контрактов командлеты можно используйте для подключения к вашей блокчейн конечной точки смарт-контрактов отвечают за управление консорциума.

### <a name="import-consortiummanagementcontracts"></a>Импорт ConsortiumManagementContracts

Подключается к консорциума управления смарт-контрактов, которые используются для элементов в пределах консорциума реализовать и управлять им.

```powershell
Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| RootContractAddress | Параметр корневого адреса контракта, консорциума управления смарт-контрактов | Yes |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Yes |

**Пример**

```powershell
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Импорт Web3Account

Чтобы создать объект, содержащий сведения учетной записи управления удаленным узлом с помощью этого командлета.

```powershell
Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Адрес учетной записи участника Блокчейна | Yes |
| ManagedAccountPassword | Пароль учетной записи адреса | Yes |

**Пример**

```powershell
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Новый Web3Connection

Устанавливает подключение к конечной точке RPC узла транзакции.

```powershell
New-Web3Connection [-RemoteRPCEndpoint <String>]
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Адрес конечной точки участника Блокчейна | Yes |


**Пример**

```powershell
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Управление участниками консорциума

Для управления элементов в пределах консорциума используйте командлеты управления консорциума член. Доступные действия зависят от вашей роли консорциума.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Получает сведения об участнике или отображать список членов консорциума.

```powershell
Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя члена службы Блокчейн в Azure, вы хотите получить сведения о на. Если имя элемента указано, возвращаются сведения элемента. Если имя указано, возвращается список всех членов консорциума. | Нет  |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Yes |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Yes |

**Пример**

```powershell
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

```powershell
Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя элемента для удаления | Yes |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Yes |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Yes |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Yes |

**Пример**

```powershell
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>SET-BlockchainMember

Задает атрибуты элементов, включая отображаемое имя и консорциум роль блокчейна.

Консорциум администраторы могут устанавливать **DisplayName** и **роли** для всех элементов. Консорциум члена роли пользователя, можно изменять только свои собственные член отображаемое имя.

```powershell
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя члена блокчейна | Yes |
| DisplayName | Новое отображаемое имя | Нет  |
| AccountAddress | Адрес учетной записи | Нет  |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Yes |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Yes |
| Web3Client |  Объект Web3Client, полученный из Нью-Web3Connection| Yes |

**Пример**

```powershell
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Управление приглашение участниками консорциума

Используйте консорциума член приглашение управления командлеты для управления консорциума член приглашения. Доступные действия зависят от вашей роли консорциума.

### <a name="new-blockchainmemberinvitation"></a>Новый BlockchainMemberInvitation

Пригласите новые элементы к консорциума.

```powershell
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure приглашенного участника | Yes |
| Роль | Консорциум роль. Значения могут быть АДМИНИСТРАТОРОМ или ПОЛЬЗОВАТЕЛЕМ. АДМИНИСТРАТОР — это роль администратора консорциума. ПОЛЬЗОВАТЕЛЬ является консорциума члена роли. | Yes |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Yes |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Yes |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Yes |

**Пример**

```powershell
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Получает или перечисляет состояние приглашения консорциума элемента.

```powershell
Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure приглашенного участника. Если идентификатор подписки не указан, возвращаются сведения о приглашении идентификатора подписки. Если идентификатор подписки указан, возвращается список всех приглашений члена. | Нет  |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Yes |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Yes |

**Пример**

```powershell
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

```powershell
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure члена для отзыва | Yes |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Yes |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Yes |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Yes |

**Пример**

```powershell
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Наборы **роли** для существующих приглашения. Только консорциума могут быть изменены приглашения.

```powershell
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | ОПИСАНИЕ | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure приглашенного участника | Yes |
| Роль | Новая роль консорциума приглашения. Значения могут быть **пользователя** или **АДМИНИСТРАТОРА** | Yes |
| Участники |  Объект элементов, полученный из ConsortiumManagementContracts импорта | Yes |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Yes |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Yes |

**Пример**

```powershell
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о консорциумы, члены и узлов см. в разделе:

> [!div class="nextstepaction"]
> [Azure Service Блокчейн-консорциума](consortium.md)