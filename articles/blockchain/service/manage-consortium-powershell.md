---
title: Управляйте членами блокчейн-сервиса Azure - PowerShell
description: Узнайте, как управлять членами консорциума Azure Blockchain Service с помощью Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505981"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Управление членами консорциума в Azure Blockchain Service с помощью PowerShell

Вы можете использовать PowerShell для управления членами блокчейн-консорциума для службы блокчейн Azure. Участники, имеющие привилегии администратора, могут приглашать, добавлять, удалять и менять роли для всех участников консорциума блокчейн. Участники, имеющие пользовательские привилегии, могут просматривать всех участников консорциума блокчейн и менять имя отображения.

## <a name="prerequisites"></a>Предварительные требования

* Создайте блокчейн-участника с помощью [портала Azure.](create-member.md)
* Для получения дополнительной информации о консорциумах, членах и узлах [см.](consortium.md)

## <a name="open-azure-cloud-shell"></a>Открытие Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

Вы также можете открыть Cloud Shell в отдельной вкладке браузера, перейдя [shell.azure.com/powershell.](https://shell.azure.com/powershell) Выберите **Copy,** чтобы скопировать блоки кода, вставьте его в облачную оболочку и выберите **Enter** для его запуска.

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Установите пакет Microsoft.AzureBlockchainService.ConsortiumManagement.PS из галереи PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Установка информационного предпочтения

Вы можете получить больше информации при выполнения cmdlets, установив переменную предпочтения информации. По *умолчанию, $InformationPreference* установлен *на SilentlyContinue*.

Для получения дополнительной подробной информации из cmdlets, установите предпочтение в PowerShell следующим образом:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Создание подключения Web3

Чтобы управлять членами консорциума, установите подключение Web3 к конечному пункту участника Blockchain Service. Этот скрипт можно использовать для установки глобальных переменных для вызова cmdlets управления консорциумом.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Замените * \<пароль\> учетной записи Участника* паролем учетной записи участника, который вы использовали при создании участника.

Найдите другие значения на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com).
1. Перейдите на **страницу** участника Blockchain Service по умолчанию.

    ![Обзор участников](./media/manage-consortium-powershell/member-overview.png)

    Замените * \<учетную запись\> участника* и * \<адрес\> RootContract* значениями с портала.

1. Для адреса конечных точек выберите **узлы транзакции,** а затем выберите **узлы транзакции по умолчанию.** Узла по умолчанию имеет то же имя, что и у участника блокчейна.
1. Выберите **строки подключения.**

    ![Строки подключения](./media/manage-consortium-powershell/connection-strings.png)

    Замените * \<адрес\> endpoint* значением от **HTTPS (ключ доступа 1)** или **HTTPS (ключ доступа 2).**

## <a name="manage-the-network-and-smart-contracts"></a>Управление сетью и смарт-контрактами

Используйте сеть и смарт-контрактcmlets для установления связи с смарт-контрактами сикры блокчейн-конечной точки, отвечающими за управление консорциумом.

### <a name="import-consortiummanagementcontracts"></a>Импорт-консорциумМенеджментКонтракты

Используйте этот cmdlet для подключения к смарт-контрактам руководства консорциума. Эти контракты используются для управления и обеспечения соблюдения членов консорциума.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| RootContractАдрес | Адрес корневого контракта смарт-контрактов управления консорциумом | Да |
| Web3Клиент | Объект Web3Client получен от New-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Импорт-Web3Счет

Используйте этот cmdlet для создания объекта для хранения информации для учетной записи управления удаленного узла.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| УправляемыйсчетАдресадрес | Адрес учетной записи участника Blockchain | Да |
| УправляемыйучетУчет | Пароль адреса учетной записи | Да |

#### <a name="example"></a>Пример

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Используйте этот cmdlet для установления соединения с конечной точкой RPC узла транзакции.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Адрес конечных точек участника Blockchain | Да |

#### <a name="example"></a>Пример

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Управление членами консорциума

Используйте cmdlets-управления членами консорциума для управления членами в рамках консорциума. Доступные действия зависят от роли консорциума.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Используйте этот cmdlet, чтобы получить сведения о членах или список членов консорциума.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| name | Имя участника службы Blockchain, о которой вы хотите получить подробную информацию. Когда имя вводится, оно возвращает данные участника. Когда имя опущено, он возвращает список всех членов консорциума. | нет |
| Участники | Объект членов, полученный от импортно-консорциумаManagementContracts | Да |
| Web3Клиент | Объект Web3Client получен от New-Web3Connection | Да |

#### <a name="example"></a>Пример

[Установите соединение Web3](#establish-a-web3-connection) для установки $ContractConnection переменной.

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

### <a name="remove-blockchainmember"></a>Удалить-BlockchainMember

Используйте этот cmdlet, чтобы удалить член blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| name | Имя участника для удаления | Да |
| Участники | Объект членов, полученный от импортно-консорциумаManagementContracts | Да |
| Web3Счет | Объект Web3Account получен из импортно-Web3Account | Да |
| Web3Клиент | Объект Web3Client получен от New-Web3Connection | Да |

#### <a name="example"></a>Пример

[Установите соединение Web3](#establish-a-web3-connection) для настройки $ContractConnection и $MemberAccount переменных.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Используйте этот cmdlet для установки атрибутов участника blockchain, включая имя дисплея и роль консорциума.

Администраторы консорциума могут устанавливать **DisplayName** и **Role** для всех участников. Член консорциума с ролью пользователя может изменять только имя дисплея своего собственного члена.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| name | Имя участника блокчейна | Да |
| DisplayName | Новое имя дисплея | нет |
| Учетная записьАдрес | Адрес учетной записи | нет |
| Участники | Объект членов, полученный от импортно-консорциумаManagementContracts | Да |
| Web3Счет | Объект Web3Account получен из импортно-Web3Account | Да |
| Web3Клиент |  Объект Web3Client получен от New-Web3Connection| Да |

#### <a name="example"></a>Пример

[Установите соединение Web3](#establish-a-web3-connection) для настройки $ContractConnection и $MemberAccount переменных.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Управление приглашениями членов консорциума

Используйте cmdlets управления приглашениями членов консорциума для управления приглашениями членов консорциума. Доступные действия зависят от роли консорциума.

### <a name="new-blockchainmemberinvitation"></a>Новый БлокчейнМон-Приглашение

Используйте этот cmdlet, чтобы пригласить новых членов в консорциум.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure участника для приглашения | Да |
| Роль | Роль консорциума. Значения могут быть ADMIN или USER. ADMIN — это роль администратора консорциума. USER является членом консорциума. | Да |
| Участники | Объект членов, полученный от импортно-консорциумаManagementContracts | Да |
| Web3Счет | Объект Web3Account получен из импортно-Web3Account | Да |
| Web3Клиент | Объект Web3Client получен от New-Web3Connection | Да |

#### <a name="example"></a>Пример

[Установите соединение Web3](#establish-a-web3-connection) для настройки $ContractConnection и $MemberAccount переменных.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Используйте этот cmdlet для получения или списка статуса приглашения члена консорциума.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure участника, который должен быть приглашен. Если идентификатор подписки предоставлен, он возвращает идентификатор приглашения. Если идентификатор подписки опущен, он возвращает список всех приглашений участника. | нет |
| Участники | Объект членов, полученный от импортно-консорциумаManagementContracts | Да |
| Web3Клиент | Объект Web3Client получен от New-Web3Connection | Да |

#### <a name="example"></a>Пример

[Установите соединение Web3](#establish-a-web3-connection) для установки $ContractConnection переменной.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Пример выходных данных

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Удалить-BlockchainMemberInvitation

Используйте этот cmdlet, чтобы отозвать приглашение члена консорциума.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure участника для отзыва | Да |
| Участники | Объект членов, полученный от импортно-консорциумаManagementContracts | Да |
| Web3Счет | Объект Web3Account получен из импортно-Web3Account | Да |
| Web3Клиент | Объект Web3Client получен от New-Web3Connection | Да |

#### <a name="example"></a>Пример

[Установите соединение Web3](#establish-a-web3-connection) для настройки $ContractConnection и $MemberAccount переменных.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Используйте этот cmdlet, чтобы настроить **роль** для существующего приглашения. Только администраторы консорциума могут изменять приглашения.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | Описание | Обязательно |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure участника для приглашения | Да |
| Роль | Новая роль консорциума для приглашения. Значения могут быть **USER** или **ADMIN**. | Да |
| Участники |  Объект членов, полученный от импортно-консорциумаManagementContracts | Да |
| Web3Счет | Объект Web3Account получен из импортно-Web3Account | Да |
| Web3Клиент | Объект Web3Client получен от New-Web3Connection | Да |

#### <a name="example"></a>Пример

[Установите соединение Web3](#establish-a-web3-connection) для настройки $ContractConnection и $MemberAccount переменных.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о консорциумах, членах и узлах [см.](consortium.md)
