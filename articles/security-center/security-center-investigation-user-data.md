---
title: Управление данными пользователя, находящимися в анализе центра безопасности Azure
description: " Сведения об управлении данными пользователя, находящимися в анализе центра безопасности Azure. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 059ca2a26e50128d6bc4313dad9f995e97c06378
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686407"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Управление данными пользователя, находящимися в анализе центра безопасности Azure
В этой статье содержатся сведения об управлении данными пользователя, находящимися в анализе центра безопасности Azure. Данные расследования хранятся в [журналах Azure Monitor](../log-analytics/log-analytics-overview.md) и доступны в центре безопасности. Управление данными пользователя включает в себя возможность удаления и экспорта данных.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Поиск персональных данных и их идентификация
На портале Azure можно использовать [функцию анализа](../security-center/security-center-investigation.md) центра безопасности, чтобы осуществлять поиск персональных данных. Функция анализа доступна в разделе **Оповещения системы безопасности**.

Все сущности, сведения о пользователе и данные отображаются на вкладке **Сущности**.

## <a name="securing-and-controlling-access-to-personal-information"></a>Защита и контроль доступа к персональным данным
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может получить доступ к данным клиента в рамках этого центра.

Дополнительные сведения о ролях читателя, владельца и участника см. в статье [Встроенные роли управления доступом на основе ролей в Azure](../role-based-access-control/built-in-roles.md). Дополнительные сведения о роли администратора учетной записи см. в статье [Добавление или изменение администраторов подписки Azure](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="deleting-personal-data"></a>Удаление персональных данных
Пользователи центра безопасности, которым назначены роли читателя, владельца, участника или администратора учетной записи, могут удалять сведения об анализе.

Чтобы удалить анализ, можно отправить запрос `DELETE` в REST API Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Входные данные `incidentName` можно найти, получив список всех инцидентов с помощью запроса `GET`.

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Экспорт персональных данных
Пользователи центра безопасности, которым назначены роли читателя, владельца, участника или администратора учетной записи, могут экспортировать сведения об анализе. Чтобы экспортировать сведения об анализе, перейдите на вкладку **Сущности**, где можно скопировать и вставить соответствующие сведения.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об управлении данными пользователя в центре безопасности Azure см. в [этой статье](security-center-privacy.md).
Дополнительные сведения об удалении личных данных в журналах Azure Monitor см. в разделе [Экспорт и удаление закрытых данных](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
