---
title: Интеграция Key Vault с центром сертификации DigiCert
description: Сведения об интеграции Key Vault с центром сертификации DigiCert
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: d6cb8d386efbcdaf053f0740d955b249c0e636b4
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907831"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Интеграция Key Vault с центром сертификации DigiCert

Azure Key Vault позволяет легко подготавливать, администрировать и развертывать цифровые сертификаты для сети и поддерживать безопасный обмен данными между приложениями. Цифровой сертификат представляет собой электронные учетные данные для подтверждения личности в электронной транзакции. 

Пользователи Azure Key Vault могут создавать сертификаты DigiCert непосредственно в Key Vault. Key Vault обеспечит комплексное управление жизненным циклом сертификатов, выданных DigiCert, благодаря наличию доверенных партнерских отношений между Key Vault и центром сертификации DigiCert.

Более общие сведения о сертификатах см. в статье [Сведения о сертификатах Azure Key Vault](/azure/key-vault/certificates/about-certificates)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуются следующие ресурсы.
* Хранилище ключей. Вы можете использовать существующее хранилище ключей или создать новое, выполнив действия, описанные в одном из этих кратких руководств:
   - [Создание хранилища ключей с помощью интерфейса командной строки Azure](../secrets/quick-create-cli.md)
   - [Создание хранилища ключей с помощью Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Создание хранилища ключей с помощью портала Azure](../secrets/quick-create-portal.md)
*   Необходимо активировать учетную запись DigiCert CertCentral. [Зарегистрируйтесь](https://www.digicert.com/account/signup/) для получения учетной записи CertCentral.
*   Разрешения уровня администратора для учетных записей.


### <a name="before-you-begin"></a>Перед началом

Убедитесь, что у вас под рукой есть следующие сведения об учетной записи DigiCert CertCentral:
-   идентификатор учетной записи CertCentral;
-   идентификатор организации;
-   Ключ API

## <a name="adding-certificate-authority-in-key-vault"></a>Добавление центра сертификации в Key Vault 
Завершив сбор данных из учетной записи DigiCert CertCentral, добавьте DigiCert в список центров сертификации в хранилище ключей.

### <a name="azure-portal"></a>Портал Azure

1.  Чтобы добавить центр сертификации DigiCert, перейдите к хранилищу ключей, в которое нужно добавить DigiCert. 
2.  На странице свойств Key Vault выберите **Сертификаты**.
3.  Выберите вкладку **Центры сертификации**. ![Свойства сертификатов](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Выберите действие **Добавить**.
 ![Свойства сертификатов](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  На экране **Создание ЦС** выберите следующие значения:
    -   **Name** (Имя). Добавьте понятное имя издателя. Например, DigicertCA.
    -   **Предоставить.** В раскрывающемся меню выберите DigiCert.
    -   **Идентификатор учетной записи.** Введите идентификатор учетной записи DigiCert CertCentral.
    -   **Пароль учетной записи.** Введите ключ API, созданный для учетной записи DigiCert CertCentral.
    -   **Идентификатор организации.** Введите значение OrgID, полученное из учетной записи DigiCert CertCentral. 
    -   Нажмите кнопку **Создать**.
   
6.  Вы увидите, что DigicertCA появился в списке центров сертификации.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell используется для создания ресурсов Azure и управления ими с помощью скриптов и команд. В Azure есть интерактивная оболочка Azure Cloud Shell, с которой можно работать на портале Azure через браузер.

Чтобы установить и использовать PowerShell локально для работы с этим руководством, понадобится модуль Azure PowerShell 1.0.0 или более поздней версии. Чтобы узнать версию, введите `$PSVersionTable.PSVersion`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzAccount`, чтобы создать подключение к Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Создайте **группу ресурсов**.

Создайте группу ресурсов Azure с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Создайте **хранилище ключей**.

Для хранилища ключей необходимо указать уникальное имя. В этом руководстве для хранилища ключей используется имя Contoso-Vaultname.

- **Имя хранилища**: Contoso-Vaultname.
- **Имя группы ресурсов**: ContosoResourceGroup.
- **Расположение**: EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Определите переменные для сведений, полученных из учетной записи DigiCert CertCentral.

- Определите переменную **Account ID**.
- Определите переменную **Org ID**.
- Определите переменную **API Key**.
- Определите переменную **Issuer Name**.

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzureKeyVaultCertificateOrganizationDetails -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
$issuerName = "DigiCertCA"
```

4. Укажите значение **Issuer**. Это действие добавляет Digicert в качестве центра сертификации в хранилище ключей.
```azurepowershell-interactive
Set-AzureKeyVaultCertificateIssuer -VaultName $vaultName -IssuerName $issuerName -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org
```

5. **Настройте политику для сертификата и реализуйте выдачу сертификата** DigiCert непосредственно из Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName DigiCertCA -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Теперь сертификат успешно выдан центром сертификации Digicert в указанном хранилище ключей через механизмы интеграции.

## <a name="troubleshoot"></a>Диагностика

Если выданный сертификат имеет состояние disabled (отключено) на портале Azure, изучите инструкции **по работе с сертификатами**, чтобы просмотреть сообщение DigiCert об ошибке, связанной с этим сертификатом.

 ![Свойства сертификатов](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Дополнительные сведения о работе с сертификатами см. в [справочнике по работе с Azure Key Vault с помощью REST API](/rest/api/keyvault). Сведения об установке разрешений см. в статьях [Vaults — Create Or Update](/rest/api/keyvault/vaults/createorupdate) (Хранилища. Создание или обновление) и [Vaults — Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Хранилища. Обновление политики доступа).

## <a name="next-steps"></a>Дальнейшие действия

- [Аутентификация, запросы и ответы](../general/authentication-requests-and-responses.md)
- [Руководство разработчика Azure Key Vault](../general/developers-guide.md)
