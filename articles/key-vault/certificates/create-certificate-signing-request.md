---
title: Создание и слияние CSR в Azure Key Vault
description: Создание и слияние CSR в Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: ae2d6259bac6a2034edc98de9b0405f32f17fbc3
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849498"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Создание и слияние CSR в Key Vault

Azure Key Vault поддерживает хранение в хранилище ключей цифрового сертификата, выданного любым центром сертификации. Он поддерживает создание запроса на подписывание сертификата с парой из закрытого и открытого ключа, а также организацию подписывания выбранным вами центром сертификации (ЦС). Это может быть внутренний ЦС предприятия или внешний общедоступный ЦС. Запрос на подписывание сертификата (CSR) — это сообщение от пользователя в центр сертификации (ЦС) с просьбой о выдаче цифрового сертификата.

Более общие сведения о сертификатах см. в статье [Сведения о сертификатах Azure Key Vault](/azure/key-vault/certificates/about-certificates).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>Добавление в KeyVault сертификата, выданного недоверенным центром сертификации

Приведенные ниже инструкции помогут вам создать сертификат в центре сертификации, не имеющем партнерских отношений с Key Vault (например, GoDaddy не является доверенным ЦС хранилища ключей). 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Сначала **создайте политику сертификата**. В этом сценарии Key Vault не будет обращаться к издателю для регистрации или обновления сертификата от имени пользователя, так как этот сценарий не поддерживает выбранный ЦС. Это подтверждается значением Unknown (Неизвестно) для параметра IssuerName (Имя издателя).

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Создание **запроса на подписывание сертификата**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Получение **подписанного ЦС запроса** CSR. Объект `$certificateOperation.CertificateSigningRequest` содержит запрос на подписывание сертификата, закодированный в формате base4. Вы можете взять этот готовый большой двоичный объект и передать его на веб-сайт издателя, принимающий запросы на сертификаты. Конкретная процедура будет зависеть от определенного центра сертификации. Поэтому рекомендации по ее выполнению лучше всего получить в самом центре сертификации. Кроме того, вы можете использовать такие средства, как certreq или openssl, чтобы подписать запрос сертификата и завершить его создание.


4. Выполните **слияние подписанного запроса** в Key Vault. Когда запрос сертификата будет подписан издателем, вы можете получить подписанный сертификат и объединить его с исходной парой закрытого и открытого ключей, созданной в Azure Key Vault.

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    Теперь для запроса на сертификат успешно выполнено слияние.

### <a name="azure-portal"></a>Портал Azure

1.  Чтобы создать CSR для выбранного ЦС, перейдите к хранилищу ключей, в которое решили добавить этот сертификат.
2.  На странице свойств Key Vault выберите **Сертификаты**.
3.  Выберите вкладку **Generate/Import** (Создать или импортировать).
4.  На экране **Создание сертификата** выберите следующие значения:
    - **Метод создания сертификата:** Сформировать.
    - **Имя сертификата:** ContosoManualCSRCertificate.
    - **Тип центра сертификации (ЦС):** Сертификат, выданный неинтегрированным ЦС
    - **Тема:** `"CN=www.contosoHRApp.com"`
    - При желании выберите другие значения. Нажмите кнопку **Создать**.

    ![Свойства сертификатов](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Вы увидите, что этот сертификат появился в списке сертификатов. Выберите этот только что созданный сертификат. Текущее состояние сертификата — "отключено", так как он еще не выдан центром сертификации.
7. Щелкните вкладку **Операции с сертификатом** и выберите **Загрузить CSR**.
 ![Свойства сертификата](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Передайте CSR-файл в центр сертификации с запросом на получение подписи.
9.  После того как запрос будет подписан ЦС, вернитесь к файлу сертификата, чтобы **выполнить слияние для подписанного запроса** на том же экране "Операция с сертификатом".

Теперь для запроса на сертификат успешно выполнено слияние.

## <a name="troubleshoot"></a>Диагностика

Если выданный сертификат имеет состояние "отключено" на портале Azure, изучите инструкции по **работе с сертификатами**, чтобы просмотреть сообщение об ошибке, связанной с этим сертификатом.

Дополнительные сведения о работе с сертификатами см. в [справочнике по работе с Azure Key Vault с помощью REST API](/rest/api/keyvault). Сведения об установке разрешений см. в статьях [Vaults — Create Or Update](/rest/api/keyvault/vaults/createorupdate) (Хранилища. Создание или обновление) и [Vaults — Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Хранилища. Обновление политики доступа).

## <a name="next-steps"></a>Дальнейшие действия

- [Аутентификация, запросы и ответы](../general/authentication-requests-and-responses.md)
- [Руководство разработчика Azure Key Vault](../general/developers-guide.md)
