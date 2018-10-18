---
title: Руководство. Получение данных, используя API отчетов Azure AD с сертификатами | Документы Майкрософт
description: В этом руководстве описывается использования API отчетов Azure AD с учетными данными сертификатов для получения данных из каталогов без вмешательства пользователя.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 5c54af76fc1e145ea062c6bcb37f354a7de94781
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364182"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Руководство. Получение данных с помощью API отчетов Azure Active Directory с сертификатами

[API-интерфейсы отчетов Azure Active Directory (Azure AD)](concept-reporting-api.md) предоставляют программный доступ к данным с помощью набора API-интерфейсов на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования. Чтобы получить доступ к API отчетов Azure AD без вмешательства пользователя, необходимо настроить доступ с помощью сертификатов.

В этом руководстве вы узнаете, как создать тестовый сертификат и использовать его для доступа к MS Graph API для создания отчетов. Мы не рекомендуем использовать тестовый сертификат в рабочей среде. 

## <a name="prerequisites"></a>Предварительные требования

1. Сначала выполните [Предварительные требования для доступа к API отчетов Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

2. Скачайте и установите [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

3. Установите [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Этот модуль предоставляет несколько служебных командлетов, позволяющих получить:
    - Библиотеки ADAL, необходимые для проверки подлинности
    - маркеры доступа пользователя, ключи приложений и сертификаты с использованием ADAL;
    - обработку результатов с разбивкой на страницы с помощью API Graph.

4. Если вы используете модуль впервые, выполните командлет **Install-MSCloudIdUtilsModule** или же импортируйте его с помощью команды PowerShell **Import-Module**.

Сеанс должен выглядеть так, как показано ниже.

  ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
## <a name="create-a-test-certificate"></a>Создание тестового сертификата

1. Используйте командлет Powershell **New-SelfSignedCertificate** для создания тестового сертификата.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

2. Используйте командлет **Export-Certificate**, чтобы экспортировать его в файл сертификата.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="register-the-certificate-in-your-app"></a>Регистрация сертификата в приложении

1. Перейдите на [портал Azure](https://portal.azure.com), выберите **Azure Active Directory**, затем выберите **Регистрация приложений** и ваше приложение из списка. 

2. Выберите **Параметры** > **Ключи**, а затем **Отправить открытый ключ**.

3. Выберите файл сертификата из предыдущего шага и нажмите **Сохранить**. 

4. Запишите идентификатор приложения и отпечаток сертификата, который вы только что зарегистрировали для приложения. Чтобы найти отпечаток, на странице приложения на портале щелкните **Параметры** и выберите пункт **Ключи**. Отпечаток будет указан в списке **Открытые ключи**.

5. Откройте манифест приложения во встроенном редакторе манифеста и замените свойство *keyCredentials* данными нового сертификата, используя следующую схему. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Сохраните манифест. 
  
## <a name="get-an-access-token-for-ms-graph-api"></a>Получение маркера доступа для API Microsoft Graph

1. Используйте командлет **Get MSCloudIdMSGraphAccessTokenFromCert** из PowerShell-модуля MSCloudIdUtils, передав идентификатор приложения и отпечаток, полученный на предыдущем шаге. 

 ![Портал Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>использовать маркер доступа для вызова API Graph.

1. Теперь маркер доступа можно использовать в скрипте PowerShell для запроса API Graph. Используйте командлет **Invoke-MSCloudIdMSGraphQuery** из модуля MSCloudIDUtils для перечисления операций входа и запроса конечной точки diectoryAudits. Этот командлет обрабатывает результаты с разбивкой на несколько страниц и отправляет их в конвейер PowerShell.

2. Запросите конечную точку directoryAudits для получения журналов аудита. 
 ![портала Azure](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

3. Запросите конечную точку signins для получения журналов входа в систему.
 ![портала Azure](./media/tutorial-access-api-with-certificates/query-signins.png)

4. Теперь можно экспортировать эти данные в CSV-файл и сохранить его в системе SIEM. Также можно перенести скрипт в запланированную задачу, чтобы периодически получать данные Azure AD из клиента без необходимости сохранять ключи приложений в исходном коде. 

## <a name="next-steps"></a>Дополнительная информация

* [Ознакомление с API отчетов](concept-reporting-api.md)
* [Справочник по API аудита](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Справочник по API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



