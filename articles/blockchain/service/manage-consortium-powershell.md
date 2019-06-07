---
title: Управлять участниками консорциума Блокчейн службы Azure с помощью Azure PowerShell
description: Узнайте, как управление участниками консорциума Блокчейн службы Azure с помощью Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493661"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Управление участниками консорциума в службе Блокчейн в Azure с помощью PowerShell

Можно использовать PowerShell позволяют управлять участниками консорциума блокчейн, Блокчейн службы Azure. Элементы, обладающие правами администратора может пригласить, добавить, удалить и изменить роли всех участников в блокчейн-консорциума. Элементы, обладающие правами пользователя можно просматривать все участники блокчейн-консорциума и изменять их отображаемое имя члена.

## <a name="prerequisites"></a>Технические условия

* Создание элемента блокчейн с помощью [портала Azure](create-member.md).
* Дополнительные сведения о консорциумы, члены и узлов, см. в разделе [службе Блокчейн-консорциума](consortium.md).

## <a name="open-azure-cloud-shell"></a>Открытие Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

Можно также открыть Cloud Shell в отдельной вкладке браузера, перейдя к [shell.azure.com/powershell](https://shell.azure.com/powershell). Выберите **копирования** для копирования блоков кода, вставьте его в Cloud Shell и выберите **ввод** для его запуска.

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Установите пакет Microsoft.AzureBlockchainService.ConsortiumManagement.PS из коллекции PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Настроить параметры сведения

Дополнительные сведения можно получить при выполнении командлетов, задав привилегированной переменной сведения. По умолчанию *$InformationPreference* присваивается *SilentlyContinue*.

Более подробные сведения из командлетов задания предпочтения в PowerShell следующим образом:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Установить соединение Web3

Позволяют управлять участниками консорциума, подключения Web3 к конечной точке службы Блокчейн член. Чтобы задать глобальные переменные для вызова командлетов управления консорциума можно использовать этот скрипт.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Замените *\<пароль учетной записи члена\>* паролем учетной записи члена, который использовался при создании элемента.

Найти другие значения на портале Azure:

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите к службе Блокчейн член по умолчанию **Обзор** страницы.

    ![Обзор членов](./media/manage-consortium-powershell/member-overview.png)

    Замените *\<владелец учетной записи\>* и *\<адрес RootContract\>* со значениями с портала.

1. Адрес конечной точки, выберите **узлы транзакции**, а затем выберите **узла транзакций по умолчанию**. Узел по умолчанию имеет имя, совпадающее с именем члена блокчейна.
1. Выберите элемент **Строки подключения**.

    ![Строки подключения](./media/manage-consortium-powershell/connection-strings.png)

    Замените *\<адрес конечной точки\>* со значением из **HTTPS (ключ доступа 1)** или **HTTPS (ключ доступа 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Управление сетью и смарт-контрактов

Сети и смарт-контрактов командлеты можно используйте для подключения к конечной точки блокчейн смарт-контрактов, отвечающие за управление консорциума.

### <a name="import-consortiummanagementcontracts"></a>Импорт ConsortiumManagementContracts

Для подключения к смарт-контрактов консорциума управления с помощью этого командлета. Эти контракты позволяют реализовать элементов в пределах консорциума и управлять им.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| RootContractAddress | Корневой адрес контракта консорциума управления смарт-контрактов | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Импорт Web3Account

Чтобы создать объект, содержащий данные для учетной записи управления на удаленном узле с помощью этого командлета.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Адрес учетной записи участника Блокчейна | Да |
| ManagedAccountPassword | Пароль учетной записи адреса | Да |

#### <a name="example"></a>Пример

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Новый Web3Connection

Для подключения к конечной точке RPC узла транзакции с помощью этого командлета.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Адрес конечной точки участника Блокчейна | Да |

#### <a name="example"></a>Пример

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Управление участниками консорциума

Для управления элементов в пределах консорциума используйте командлеты управления консорциума член. Доступные действия зависят от вашей роли консорциума.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Чтобы получить сведения об участнике или перечислить участников соглашения о с помощью этого командлета.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя члена службы Блокчейн, который требуется получить сведения о. При вводе имени, он возвращает сведения об участнике. Если имя опущено, оно возвращает список всех членов консорциума. | Нет |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Пример выходных данных

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Используйте этот командлет для удаления члена блокчейна.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| Name | Имя элемента для удаления | Да |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>SET-BlockchainMember

Чтобы задать атрибуты элементов, включая отображаемое имя и роль консорциума блокчейн с помощью этого командлета.

Консорциум администраторы могут устанавливать **DisplayName** и **роли** для всех элементов. Консорциум члена роли пользователя, можно изменить только свои собственные член отображаемое имя.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| Name | Имя члена блокчейна | Да |
| DisplayName | Новое отображаемое имя | Нет |
| AccountAddress | Адрес учетной записи | Нет |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client |  Объект Web3Client, полученный из Нью-Web3Connection| Да |

#### <a name="example"></a>Пример

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Управлять приглашениями консорциума членов

Используйте консорциума член приглашение управления командлеты для управления члены консорциума приглашения. Доступные действия зависят от вашей роли консорциума.

### <a name="new-blockchainmemberinvitation"></a>Новый BlockchainMemberInvitation

Используйте этот командлет, чтобы пригласить новые элементы к консорциума.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure, участника на приглашение | Да |
| Роль | Роль консорциума. Значения могут быть АДМИНИСТРАТОРОМ или ПОЛЬЗОВАТЕЛЕМ. АДМИНИСТРАТОР — это роль администратора консорциума. ПОЛЬЗОВАТЕЛЬ является консорциума члена роли. | Да |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Чтобы получить или перечислить членом консорциума состояние приглашения с помощью этого командлета.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure, участника для приглашения. Если идентификатор не указан, он возвращает подписки подписка идентификаторы сведения о приглашении. Если указан идентификатор подписки, он возвращает список всех приглашений члена. | Нет |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Пример выходных данных

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Отозвать приглашение участника консорциума с помощью этого командлета.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure члена отзывать | Да |
| Участники | Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

С помощью этого командлета для задания **роли** для существующих приглашения. Только консорциума могут быть изменены приглашения.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure, участника на приглашение | Да |
| Роль | Новая роль консорциума приглашения. Значения могут быть **пользователя** или **АДМИНИСТРАТОРА**. | Да |
| Участники |  Объект элементов, полученный из ConsortiumManagementContracts импорта | Да |
| Web3Account | Объект Web3Account, полученный из Web3Account импорта | Да |
| Web3Client | Объект Web3Client, полученный из Нью-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о консорциумы, члены и узлов см. в разделе:

> [!div class="nextstepaction"]
> [Azure Service Блокчейн-консорциума](consortium.md)
