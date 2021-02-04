---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e98092cfb0ae96b3d981b52094825810ab1dbfd0
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558099"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Необходимо включить шифрование для переменных учетной записи службы автоматизации](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |Обязательно включайте шифрование ресурсов переменных в учетной записи службы автоматизации при хранении конфиденциальных данных. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Учетные записи службы автоматизации Azure должны использовать управляемые клиентом ключи для шифрования неактивных данных.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |Используйте управляемые клиентом ключи для управления шифрованием неактивных учетных записей службы автоматизации Azure. По умолчанию данные клиента шифруются с помощью управляемых службой ключей, но для соблюдения нормативных требований обычно требуются управляемые клиентом ключи. Ключи, управляемые клиентом, позволяют шифровать данные с помощью Azure Key Vault ключа, созданного и принадлежащего вам. Вы полностью контролируете жизненный цикл ключа, включая его смену и управление им. Подробная информация собрана на странице [https://aka.ms/automation-cmk](https://aka.ms/automation-cmk). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
