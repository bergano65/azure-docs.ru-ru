---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5830c6a0c2e366af165a291d89e029d6226d12c8
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99215467"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Устройства Azure Stack Edge должны использовать двойное шифрование](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Для защиты неактивных данных на устройстве убедитесь, что для них используется двойное шифрование, доступ к данным контролируется, а после отключения устройства данные безопасно удаляются с дисков данных. Двойное шифрование предусматривает два уровня шифрования: 256-битное шифрование BitLocker XTS-AES для томов данных и встроенное шифрование для жестких дисков. Подробные сведения см. в документации с обзором функций безопасности для конкретного устройства Stack Edge. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
