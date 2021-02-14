---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 32d602514419f672b6c4709a0f3da41d2330e9ce
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098386"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Кластеры Azure HDInsight должны использовать ключи, управляемые клиентом, для шифрования неактивных данных](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Используйте управляемые клиентом ключи для управления шифрованием неактивных кластеров Azure HDInsight. По умолчанию данные клиента шифруются с помощью управляемых службой ключей, но для соблюдения нормативных требований обычно требуются управляемые клиентом ключи. Ключи, управляемые клиентом, позволяют шифровать данные с помощью Azure Key Vault ключа, созданного и принадлежащего вам. Вы полностью контролируете жизненный цикл ключа, включая его смену и управление им. Подробная информация собрана на странице [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk). |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Кластеры Azure HDInsight должны использовать шифрование на узле для шифрования неактивных данных](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Включение шифрования на узле помогает защитить данные согласно корпоративным обязательствам по обеспечению безопасности и соответствия. При включении шифрования на узле данные, хранящиеся на узле виртуальной машины, шифруются при хранении и передаются в зашифрованном виде в службу хранилища. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Кластеры Azure HDInsight должны использовать шифрование при передаче для шифрования обмена данными между узлами кластеров Azure HDInsight](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Данные могут быть изменены во время передачи между узлами кластеров Azure HDInsight. Включение шифрования при передаче устраняет проблемы несанкционированного использования и незаконного изменения данных во время передачи. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
