---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8f09bc58c536dc7262405a79c1dedacf76b6ffa0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096267"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Необходимо включить шифрование для переменных учетной записи службы автоматизации](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |Обязательно включайте шифрование ресурсов переменных в учетной записи службы автоматизации при хранении конфиденциальных данных. |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Учетные записи службы автоматизации Azure должны использовать управляемые клиентом ключи для шифрования неактивных данных.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |Используйте управляемые клиентом ключи для управления шифрованием неактивных учетных записей службы автоматизации Azure. По умолчанию данные клиента шифруются с помощью управляемых службой ключей, но для соблюдения нормативных требований обычно требуются управляемые клиентом ключи. Ключи, управляемые клиентом, позволяют шифровать данные с помощью Azure Key Vault ключа, созданного и принадлежащего вам. Вы полностью контролируете жизненный цикл ключа, включая его смену и управление им. Подробная информация собрана на странице [https://aka.ms/automation-cmk](https://aka.ms/automation-cmk). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
