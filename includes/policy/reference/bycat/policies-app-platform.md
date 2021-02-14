---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d85b264544db647ded03af20f2adade79b5b425b
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100396"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Audit Azure Spring Cloud instances where distributed tracing is not enabled](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) (Проверка экземпляров Azure Spring Cloud с отключенной распределенной трассировкой) |Средства распределенной трассировки в Azure Spring Cloud позволяют разработчикам выполнять отладку и мониторинг сложных взаимодействий между микрослужбами в приложении. Распределенные средства трассировки должны быть включены и находиться в работоспособном состоянии. |Audit, Disabled |[1.0.0 (предварительная версия)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Служба Azure Spring Cloud должна использовать внедрение сети](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Экземпляры Azure Spring Cloud должны использовать внедрение виртуальной сети в следующих целях: 1. Изоляция Azure Spring Cloud от Интернета. 2. Реализация взаимодействия Azure Spring Cloud с системами в локальных центрах обработки данных или службах Azure в других виртуальных сетях. 3. Предоставление клиентам возможности контролировать входящие и исходящие сетевые подключения для Azure Spring Cloud. |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
