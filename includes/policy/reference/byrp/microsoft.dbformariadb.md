---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c4619610cc8e54f9fbe556e3d12668a680a2e261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859324"
---
|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[База данных Azure для MariaDB должна использовать геоизбыточное резервное копирование](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |База данных Azure для MariaDB позволяет выбрать параметр избыточности для сервера базы данных. Можно задать геоизбыточное хранилище резервных копий, в котором данные хранятся не только в том регионе, в котором размещен сервер, но и в парном регионе для обеспечения восстановления в случае сбоя региона. Настройка геоизбыточного хранилища для резервного копирования разрешена только во время создания сервера. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Сервер MariaDB должен использовать конечную точку службы виртуальной сети.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Правила брандмауэра на основе виртуальной сети используются для включения трафика из определенной подсети в базу данных Azure для MariaDB, при этом трафик остается в пределах границы Azure. Эта политика предоставляет способ аудита, если в базе данных Azure для MariaDB используется конечная точка службы виртуальной сети. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Для серверов MariaDB необходимо включить частную конечную точку.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Подключения к частным конечным точкам обеспечивают безопасное соединение, обеспечивая возможность частного подключения к базе данных Azure для MariaDB. Настройте подключение к частной конечной точке, чтобы разрешить доступ к трафику, поступающему только из известных сетей, и запретите доступ со всех других IP-адресов, включая в Azure. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Для северов MariaDB должен быть отключен доступ через общедоступную сеть](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Отключение свойства доступа к общедоступной сети повышает безопасность, обеспечивая доступ к базе данных Azure для MariaDB только из частной конечной точки. Эта конфигурация строго отключает доступ из любого общедоступного адресного пространства за пределами диапазона IP-адресов Azure и запрещает все имена входа, соответствующие правилам брандмауэра на основе IP или виртуальной сети. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
