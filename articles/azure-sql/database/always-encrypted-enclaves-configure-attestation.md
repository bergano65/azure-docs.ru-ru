---
title: Настройка аттестации Azure для логического сервера Azure SQL
description: Настройте аттестацию Azure для Always Encrypted с помощью Secure енклавес в базе данных SQL Azure.
keywords: Шифрование данных, шифрование SQL, шифрование базы данных, конфиденциальные данные, Always Encrypted, безопасный енклавес, SGX, аттестация
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: e8cb423d4d700c4b6b6caa30a02eac3e7ef10cb6
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253577"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Настройка аттестации Azure для логического сервера Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted с безопасным енклавес для базы данных SQL Azure в настоящее время находится в **общедоступной предварительной версии**.

[Microsoft Azure аттестация](../../attestation/overview.md) — это решение для подтверждения доверенных сред выполнения (TEEs), включая расширения Intel Software Guard (Intel SGX) енклавес. 

Чтобы использовать аттестацию Azure для аттестации Intel SGX енклавес, используемой для [Always encrypted с безопасными енклавес](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) в базе данных SQL Azure, необходимо:

1. Создайте [поставщик аттестации](../../attestation/basic-concepts.md#attestation-provider) и настройте его с помощью рекомендованной политики аттестации.

2. Предоставьте логическому серверу SQL Azure доступ к поставщику аттестации.

> [!NOTE]
> Настройка аттестации несет ответственность за администратора аттестации. Ознакомьтесь с [ролями и обязанностями при настройке енклавес и аттестации SGX](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Требования

Логический сервер SQL Azure и поставщик аттестации должны принадлежать одному и тому же клиенту Azure Active Directory. Взаимодействия между клиентами не поддерживаются. 

Логическому серверу SQL Azure должно быть назначено удостоверение Azure AD. Администратор аттестации должен получить идентификатор Azure AD сервера у администратора базы данных SQL Azure для этого сервера. Удостоверение будет использоваться для предоставления серверу доступа к поставщику аттестации. 

Инструкции по созданию сервера с удостоверением или назначению удостоверения для существующего сервера с помощью PowerShell и Azure CLI см. в статье [назначение удостоверения Azure AD](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server)серверу.

## <a name="create-and-configure-an-attestation-provider"></a>Создание и настройка поставщика аттестации

[Поставщик аттестации](../../attestation/basic-concepts.md#attestation-provider) — это ресурс в аттестации Azure, который оценивает [запросы аттестации](../../attestation/basic-concepts.md#attestation-request) для [политик аттестации](../../attestation/basic-concepts.md#attestation-request) и выдает [маркеры аттестации](../../attestation/basic-concepts.md#attestation-token). 

Политики аттестации указываются с помощью [грамматики правила утверждений](../../attestation/claim-rule-grammar.md).

Корпорация Майкрософт рекомендует следующую политику для аттестации енклавес Intel SGX, используемой для Always Encrypted в базе данных SQL Azure.

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

Указанная выше политика проверяет следующее.

- Анклава в базе данных SQL Azure не поддерживает отладку (что сокращает уровень защиты, предоставляемый анклава).
- Идентификатор продукта библиотеки в анклава — это идентификатор продукта, назначаемый Always Encrypted с безопасным енклавес (4639).
- Идентификатор версии (SVN) библиотеки больше 0.
- Библиотека в анклава была подписана с помощью ключа подписывания Майкрософт (значение утверждения x-MS-SGX-мрсигнер — это хэш ключа подписывания).

> [!IMPORTANT]
> Поставщик аттестации создается с политикой по умолчанию для Intel SGX енклавес, которая не проверяет код, выполняемый в анклава. Корпорация Майкрософт настоятельно рекомендует задать рекомендуемую политику и не использовать политику по умолчанию для Always Encrypted с Secure енклавес.

Инструкции по созданию поставщика аттестации и его настройке с помощью политики аттестации:

- [Краткое руководство. Настройка аттестации Azure с помощью портал Azure](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > При настройке политики аттестации с портал Azure установите для параметра Тип аттестации значение `SGX-IntelSDK` .
- [Краткое руководство. Настройка службы "Аттестация Azure" с помощью Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > При настройке политики аттестации с помощью Azure PowerShell задайте `Tee` для параметра значение `SgxEnclave` .
- [Краткое руководство. Настройка службы "Аттестация Azure" с помощью Azure CLI](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > При настройке политики аттестации с помощью Azure CLI задайте `attestation-type` для параметра значение `SGX-IntelSDK` .

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Определение URL-адреса аттестации для политики аттестации

После настройки политики аттестации необходимо предоставить URL-адрес аттестации, ссылающийся на политику, администраторов приложений, использующих Always Encrypted с защищенным енклавес в базе данных SQL Azure. Администраторам приложений и/или пользователям приложений потребуется настроить свои приложения с помощью URL-адреса аттестации, чтобы они могли выполнять инструкции, использующие Secure енклавес.

### <a name="use-powershell-to-determine-the-attestation-url"></a>Определение URL-адреса аттестации с помощью PowerShell

Чтобы определить URL-адрес аттестации, используйте следующий скрипт:

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Определение URL-адреса аттестации с помощью портал Azure

1. В области Обзор для поставщика аттестации скопируйте значение свойства "URI аттестации" в буфер обмена. URI аттестации должен выглядеть следующим образом: `https://MyAttestationProvider.us.attest.azure.net` .

2. Добавьте следующий код в URI аттестации: `/attest/SgxEnclave` . 

Итоговый URL-адрес аттестации должен выглядеть следующим образом: `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Предоставление логическому серверу SQL Azure доступа к поставщику аттестации

Во время рабочего процесса аттестации логический сервер SQL Azure, содержащий базу данных, вызывает поставщик аттестации для отправки запроса аттестации. Чтобы логический сервер SQL Azure мог отправлять запросы аттестации, сервер должен иметь разрешение для `Microsoft.Attestation/attestationProviders/attestation/read` действия в поставщике аттестации. Рекомендуемый способ предоставить разрешение для администратора поставщика аттестации, чтобы назначить удостоверение Azure AD сервера роли читателя аттестации для поставщика аттестации или содержащей его группу ресурсов.

### <a name="use-azure-portal-to-assign-permission"></a>Назначение разрешения с помощью портал Azure

Чтобы назначить удостоверение сервера Azure SQL Server роли читателя аттестации для поставщика аттестации, следуйте указаниям в разделе [Добавление и удаление назначений ролей Azure с помощью портал Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). В области **Добавление назначения ролей** выполните следующие действия.

1. В раскрывающемся списке **роль** выберите роль **читатель аттестации** .
1. В поле **Выбор** введите имя сервера Azure SQL Server для поиска.

Пример см. на снимке экрана ниже.

![назначение роли читателя аттестации](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> Чтобы сервер отображался на панели **Добавление назначения ролей** , на сервере должно быть назначено удостоверение Azure AD — см. [требования](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Назначение разрешения с помощью PowerShell

1. Найдите логический сервер SQL Azure.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. Назначьте сервер роли читателя аттестации для группы ресурсов, содержащей поставщика аттестации.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

Дополнительные сведения см. в статье [Добавление и удаление назначений ролей Azure с помощью Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#add-a-role-assignment).

## <a name="next-steps"></a>Next Steps

- [Управление ключами для Always Encrypted с безопасными анклавами](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>См. также

- [Руководство. Приступая к работе с Always Encrypted Secure енклавес в базе данных SQL Azure](always-encrypted-enclaves-getting-started.md)