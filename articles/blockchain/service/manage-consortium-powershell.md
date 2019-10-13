---
title: Управление членами в консорциуме Блокчейн службы Azure с помощью Azure PowerShell
description: Узнайте, как управлять членами консорциума Блокчейн службы Azure с помощью Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 3e149a4a8e5ce7c82f0c9bf951bf9625763b30af
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285994"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Управление членами консорциума в службе Блокчейн Azure с помощью PowerShell

Вы можете использовать PowerShell для управления членами блокчейн Consortium в службе Блокчейн Azure. Участники, имеющие права администратора, могут приглашать, добавлять, удалять и изменять роли для всех участников в консорциуме блокчейн. Участники, имеющие привилегии пользователя, могут просматривать всех участников в консорциуме блокчейн и изменять отображаемое имя участника.

## <a name="prerequisites"></a>Предварительные требования

* Создайте элемент блокчейн с помощью [портал Azure](create-member.md).
* Дополнительные сведения о консортиа, членах и узлах см. в статье [консорциум по службам Azure блокчейн](consortium.md).

## <a name="open-azure-cloud-shell"></a>Открытие Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

Можно также открыть Cloud Shell на отдельной вкладке браузера, перейдя по адресу [Shell.Azure.com/PowerShell](https://shell.azure.com/powershell). Выберите **Копировать** , чтобы скопировать блоки кода, вставьте его в Cloud Shell и нажмите клавишу **Ввод** , чтобы запустить его.

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Установите пакет Microsoft.AzureBlockchainService.ConsortiumManagement.PS из коллекция PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Задание параметров сведений

Дополнительные сведения о выполнении командлетов можно получить, задав переменную настройки информации. По умолчанию для *$InformationPreference* задано значение *SilentlyContinue*.

Чтобы получить более подробные сведения из командлетов, задайте предпочтения в PowerShell следующим образом:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Установка подключения Web3

Чтобы управлять членами консорциума Consortium, установите подключение Web3 к конечной точке члена службы Блокчейн. С помощью этого скрипта можно задать глобальные переменные для вызова командлетов управления консорциумом.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Замените *пароль учетной записи \<Member @ no__t-2* на пароль учетной записи участника, который использовался при создании члена.

Найдите другие значения в портал Azure:

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите на страницу **обзора** членов службы блокчейн по умолчанию.

    ![Обзор членов](./media/manage-consortium-powershell/member-overview.png)

    Замените *\<Member Account @ no__t-2* и *\<RootContract Address @ no__t-5* значениями с портала.

1. В поле адрес конечной точки выберите **узлы транзакции**, а затем выберите **узел транзакция по умолчанию**. Имя узла по умолчанию совпадает с именем члена блокчейн.
1. Выберите элемент **Строки подключения**.

    ![Строки подключения](./media/manage-consortium-powershell/connection-strings.png)

    Замените *\<Endpoint Address @ no__t-2* значением из **HTTPS (ключ доступа 1)** или **HTTPS (ключ доступа 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Управление сетью и интеллектуальными контрактами

Используйте командлеты сетевого и смарт-контракта, чтобы установить подключение к смарт-контрактам конечной точки блокчейн, ответственным за управление консорциумом.

### <a name="import-consortiummanagementcontracts"></a>Import-Консортиумманажементконтрактс

Используйте этот командлет для подключения к смарт-контрактам управления консорциумом. Эти контракты используются для управления и реализации членов в консорциуме.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| рутконтрактаддресс | Адрес корневого контракта для смарт-контрактов управления консорциумом | Да |
| Web3Client | Объект Web3Client, полученный из New-Web3Connection | Да |

#### <a name="example"></a>Пример

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Используйте этот командлет, чтобы создать объект для хранения сведений об учетной записи управления удаленного узла.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| манажедаккаунтаддресс | Адрес учетной записи члена блокчейн | Да |
| манажедаккаунтпассворд | Пароль адреса учетной записи | Да |

#### <a name="example"></a>Пример

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Используйте этот командлет для установления соединения с конечной точкой RPC узла транзакции.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ремотерпцендпоинт | Адрес конечной точки члена блокчейн | Да |

#### <a name="example"></a>Пример

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Управление членами консорциума Consortium

Используйте командлеты управления членами консорциума W3C для управления членами в рамках консорциума. Доступные действия зависят от вашей роли консорциума.

### <a name="get-blockchainmember"></a>Get-Блоккчаинмембер

Используйте этот командлет для получения сведений о члене или списка членов консорциума.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя члена службы Блокчейн, сведения о котором необходимо получить. Если указано имя, оно возвращает сведения об элементе. Если имя не указано, возвращается список всех членов консорциума. | Нет |
| Members | Объект Members, полученный из Import-Консортиумманажементконтрактс | Да |
| Web3Client | Объект Web3Client, полученный из New-Web3Connection | Да |

#### <a name="example"></a>Пример

Установите [соединение Web3](#establish-a-web3-connection) , чтобы задать переменную $ContractConnection.

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

### <a name="remove-blockchainmember"></a>Remove-Блоккчаинмембер

Используйте этот командлет для удаления члена блокчейн.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя удаляемого члена | Да |
| Members | Объект Members, полученный из Import-Консортиумманажементконтрактс | Да |
| Web3Account | Объект Web3Account, полученный из Import-Web3Account | Да |
| Web3Client | Объект Web3Client, полученный из New-Web3Connection | Да |

#### <a name="example"></a>Пример

Установите [соединение Web3](#establish-a-web3-connection) , чтобы задать переменные $ContractConnection и $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-Блоккчаинмембер

Используйте этот командлет для задания атрибутов элементов блокчейн, включая отображаемое имя и роль консорциума.

Администраторы консорциума W3C могут задавать **DisplayName** и **Role** для всех элементов. Член консорциума с ролью пользователя может изменять только отображаемое имя элемента.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| ИМЯ | Имя элемента блокчейн | Да |
| DisplayName | Новое отображаемое имя | Нет |
| аккаунтаддресс | Адрес учетной записи | Нет |
| Members | Объект Members, полученный из Import-Консортиумманажементконтрактс | Да |
| Web3Account | Объект Web3Account, полученный из Import-Web3Account | Да |
| Web3Client |  Объект Web3Client, полученный из New-Web3Connection| Да |

#### <a name="example"></a>Пример

Установите [соединение Web3](#establish-a-web3-connection) , чтобы задать переменные $ContractConnection и $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Управление приглашениями членов в консорциуме Consortium

Используйте командлеты управления приглашенными участниками консорциума для управления приглашениями в отношении членов консорциума. Доступные действия зависят от вашей роли консорциума.

### <a name="new-blockchainmemberinvitation"></a>New-Блоккчаинмемберинвитатион

Используйте этот командлет, чтобы пригласить новых членов в консорциум.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure участника, которого нужно пригласить | Да |
| Role | Роль консорциума. Может принимать значения ADMIN или USER. Администратор является ролью администратора консорциума. ПОЛЬЗОВАТЕЛЬ является ролью члена консорциума. | Да |
| Members | Объект Members, полученный из Import-Консортиумманажементконтрактс | Да |
| Web3Account | Объект Web3Account, полученный из Import-Web3Account | Да |
| Web3Client | Объект Web3Client, полученный из New-Web3Connection | Да |

#### <a name="example"></a>Пример

Установите [соединение Web3](#establish-a-web3-connection) , чтобы задать переменные $ContractConnection и $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-Блоккчаинмемберинвитатион

Используйте этот командлет для получения или перечисления состояния приглашенного для члена консорциума.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure участника, которого нужно пригласить. Если указан идентификатор подписки, он возвращает сведения о приглашении для идентификатора подписки. Если идентификатор подписки пропущен, он возвращает список всех приглашений участников. | Нет |
| Members | Объект Members, полученный из Import-Консортиумманажементконтрактс | Да |
| Web3Client | Объект Web3Client, полученный из New-Web3Connection | Да |

#### <a name="example"></a>Пример

Установите [соединение Web3](#establish-a-web3-connection) , чтобы задать переменную $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Пример выходных данных

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-Блоккчаинмемберинвитатион

Используйте этот командлет для отзыва приглашения члена консорциума.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure члена, который требуется отозвать. | Да |
| Members | Объект Members, полученный из Import-Консортиумманажементконтрактс | Да |
| Web3Account | Объект Web3Account, полученный из Import-Web3Account | Да |
| Web3Client | Объект Web3Client, полученный из New-Web3Connection | Да |

#### <a name="example"></a>Пример

Установите [соединение Web3](#establish-a-web3-connection) , чтобы задать переменные $ContractConnection и $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-Блоккчаинмемберинвитатион

Используйте этот командлет, чтобы задать **роль** для существующего приглашения. Только администраторы консорциума могут изменять приглашения.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Параметр | Описание | Обязательно для заполнения |
|-----------|-------------|:--------:|
| SubscriptionId | Идентификатор подписки Azure участника, которого нужно пригласить | Да |
| Role | Новая роль консорциума для приглашения. Значения могут быть "пользователь **" или "Администратор"** . | Да |
| Members |  Объект Members, полученный из Import-Консортиумманажементконтрактс | Да |
| Web3Account | Объект Web3Account, полученный из Import-Web3Account | Да |
| Web3Client | Объект Web3Client, полученный из New-Web3Connection | Да |

#### <a name="example"></a>Пример

Установите [соединение Web3](#establish-a-web3-connection) , чтобы задать переменные $ContractConnection и $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о консортиа, членах и узлах см. в следующих статьях:

> [!div class="nextstepaction"]
> [Консорциум по службам Azure Блокчейн](consortium.md)
