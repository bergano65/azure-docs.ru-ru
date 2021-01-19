---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7952c79054b72d98b91642c5b0d6f3db4eade1ce
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047449"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Устройства Azure Stack Edge должны использовать двойное шифрование](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Для защиты неактивных данных на устройстве убедитесь, что для них используется двойное шифрование, доступ к данным контролируется, а после отключения устройства данные безопасно удаляются с дисков данных. Двойное шифрование предусматривает два уровня шифрования: 256-битное шифрование BitLocker XTS-AES для томов данных и встроенное шифрование для жестких дисков. Подробные сведения см. в документации с обзором функций безопасности для конкретного устройства Stack Edge. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
