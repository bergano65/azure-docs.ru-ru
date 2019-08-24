---
title: Защита сообщений B2B с помощью сертификатов в приложениях Azure Logic Apps | Документация Майкрософт
description: Добавление сертификатов для защиты сообщений B2B в Azure Logic Apps с помощью пакета интеграции Enterprise
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: be3dbc386b0ac89a85bc1719081df944523c28ce
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997836"
---
# <a name="secure-b2b-messages-with-certificates"></a>Защита сообщений B2B с помощью сертификатов

Когда требуется сохранять конфиденциальность связи B2B, можно защитить безопасность обмена сообщениями B2B в приложениях интеграции предприятия, а конкретно в приложениях логики, путем добавления сертификатов в учетную запись интеграции. Сертификаты — это цифровые документы, которые проверяют удостоверения участников обмена электронными данными и помогают защитить связь следующими способами.

* Шифрование содержимого сообщения.
* Цифровая подпись сообщений. 

В приложениях интеграции Enterprise можно использовать следующие сертификаты:

* [Открытые сертификаты](https://en.wikipedia.org/wiki/Public_key_certificate), которые необходимо приобрести в общедоступном через Интернет [центре сертификации (ЦС)](https://en.wikipedia.org/wiki/Certificate_authority), для которых не требуются ключи. 

* Закрытые сертификаты или [ *самозаверяющие сертификаты*](https://en.wikipedia.org/wiki/Self-signed_certificate), которые можно создать и выдать самостоятельно. При этом для них требуются закрытые ключи. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Передача общего сертификата

Чтобы использовать *открытый сертификат* в приложениях логики с возможностями "бизнес-бизнес", необходимо сначала передать его в учетную запись интеграции. После определения свойств в создаваемых [соглашениях](logic-apps-enterprise-integration-agreements.md) сертификат можно будет использовать для защиты сообщений "бизнес-бизнес".

1. Войдите на [портале Azure](https://portal.azure.com). В главном меню Azure выберите **All resources** (Все ресурсы). В поле поиска введите имя учетной записи интеграции и выберите ее, когда она появится.

   ![Поиск и выбор учетной записи интеграции](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. В разделе **Компоненты** выберите плитку **Сертификаты**.

   ![Выбор плитки "Сертификаты"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. В разделе **Сертификаты** выберите **Добавить**. В разделе **Добавление сертификата** предоставьте указанные сведения для вашего сертификата. Когда все будет готово, нажмите кнопку **ОК**.

   | Свойство | Значение | Описание | 
   |----------|-------|-------------|
   | **Name** | <*certificate-name*> | Имя сертификата, в этом примере — "publicCert" | 
   | **Тип сертификата** | Открытый | Тип вашего сертификата |
   | **Certificate** | <*certificate-file-name*> | Чтобы найти и выбрать файл сертификата, который требуется загрузить, выберите значок папки рядом с полем **Сертификат**. |
   ||||

   ![Щелкните "Добавить" и укажите данные сертификата](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   После проверки сделанного выбора Azure отправит ваш сертификат.

   ![Отображение нового сертификата в Azure](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Передача закрытого сертификата

Чтобы использовать *закрытый сертификат* в приложениях логики с возможностями "бизнес-бизнес", необходимо сначала передать этот сертификат в учетную запись интеграции. Также потребуется закрытый ключ, который необходимо сначала добавить в [хранилище Azure Key Vault](../key-vault/key-vault-get-started.md). 

После определения свойств в создаваемых [соглашениях](logic-apps-enterprise-integration-agreements.md) сертификат можно будет использовать для защиты сообщений "бизнес-бизнес".

> [!NOTE]
> Для закрытых сертификатов не забудьте добавить соответствующий открытый сертификат, который отображается в настройках **отправки и получения** [соглашения AS2](logic-apps-enterprise-integration-as2.md) для подписи и шифрования сообщений.

1. [Добавьте закрытый ключ в хранилище Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) и укажите **имя ключа**.
   
2. Авторизуйте Azure Logic Apps для выполнения операций в Azure Key Vault. Для предоставления доступа субъекту-службе Logic Apps используйте команду PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), например:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Войдите на [портале Azure](https://portal.azure.com). В главном меню Azure выберите **All resources** (Все ресурсы). В поле поиска введите имя учетной записи интеграции и выберите ее, когда она появится.

   ![Поиск учетной записи интеграции](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. В разделе **Компоненты** выберите плитку **Сертификаты**.  

   ![Выбор плитки "Сертификаты"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. В разделе **Сертификаты** выберите **Добавить**. В разделе **Добавление сертификата** предоставьте указанные сведения для вашего сертификата. Когда все будет готово, нажмите кнопку **ОК**.

   | Свойство | Значение | Описание | 
   |----------|-------|-------------|
   | **Name** | <*certificate-name*> | Имя сертификата, в этом примере — "privateCert" | 
   | **Тип сертификата** | Закрытый | Тип вашего сертификата |
   | **Certificate** | <*certificate-file-name*> | Чтобы найти и выбрать файл сертификата, который требуется загрузить, выберите значок папки рядом с полем **Сертификат**. При использовании хранилища ключей для закрытого ключа отправленный файл будет общедоступным сертификатом. | 
   | **Группа ресурсов** | <*integration-account-resource-group*> | Группа ресурсов учетной записи интеграции, в этом примере — "MyResourceGroup" | 
   | **хранилище ключей;** | <*key-vault-name*> | Имя хранилища ключей Azure |
   | **Имя ключа** | <*key-name*> | Имя ключа |
   ||||

   ![Щелкните "Добавить" и укажите данные сертификата](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   После проверки сделанного выбора Azure отправит ваш сертификат.

   ![Отображение нового сертификата в Azure](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Следующие шаги

* [Создание соглашения "бизнес-бизнес"](logic-apps-enterprise-integration-agreements.md)
