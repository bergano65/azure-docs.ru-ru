---
title: Что нового для Azure AD в правительстве Microsoft 365? | Документы Майкрософт
description: Узнайте о некоторых изменениях в Active Directory Azure (Azure AD) в экземпляре облака правительства Microsoft 365, которые могут повлиять на вас.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425550"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Что нового для активного каталога Azure в правительстве Microsoft 365

Мы внесли некоторые изменения в Active Directory Azure (Azure AD) в экземпляре облака правительства Microsoft 365, который применим к клиентам, использующим следующие службы:

- Azure для государственных организаций

- Microsoft 365 Правительство - GCC Высокий

- Microsoft 365 Правительство - DOD

Эта статья не распространяется на Microsoft 365 правительства - GCC клиентов.

## <a name="changes-to-the-initial-domain-name"></a>Изменения в первоначальном доменном имени

Во время первоначальной регистрации в вашей организации на государственную онлайн-службу Microsoft 365 вам было предложено выбрать доменное имя вашей организации. `<your-domain-name>.onmicrosoft.com` Если у вас уже есть доменное имя с суффиксом .com, ничего не изменится.

Однако, если вы подписываетесь на новую государственную службу Microsoft 365, `.us` вам будет предложено выбрать доменное имя с помощью суффикса. Таким образом, `<your-domain-name>.onmicrosoft.us`это будет .

>[!Note]
>Это изменение не распространяется на клиентов, которые управляются поставщиками облачных услуг (CP).

## <a name="changes-to-portal-access"></a>Изменения в доступе к порталу

Мы обновили конечные точки портала для правительства Microsoft Azure, Правительства Microsoft 365 - GCC High и Правительства Microsoft 365 - DoD, как показано в [таблице отображения конечных точек.](#endpoint-mapping)

Ранее клиенты могли войти в систему с помощью всемирных порталов Azure (portal.azure.com) и Office 365 (portal.office.com). С этим обновлением, клиенты должны теперь войти в использовании конкретного правительства Microsoft Azure, Microsoft 365 правительство - GCC High, и Microsoft 365 правительство - DOD порталов.

## <a name="endpoint-mapping"></a>Сопоставление конечных точек

В следующей таблице показаны конечные точки для всех клиентов:

| name | Детали конечных точек |
|------|------------------|
| Порталы |Правительство Microsoft Azure:https://portal.azure.us<p>Microsoft 365 Правительство - GCC Высокий:https://portal.office365.us<p>Microsoft 365 Правительство - ДО:https://portal.apps.mil |
| Конечная точка управления активным и каталогом Azure | https://login.microsoftonline.us |
| Microsoft График API для Microsoft 365 правительства - GCC High | https://graph.microsoft.us |
| Microsoft График API для Microsoft 365 правительство - DOD | https://dod-graph.microsoft.us |
| Конечные точки государственных служб Azure | Для получения подробной информации смотрите [руководство разработчика правительства Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Правительство - GCC Высокие конечные точки | Для получения подробной информации, [см. Office 365 Правительство США GCC Высокие конечные точки](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Правительство - DOD | Для получения подробной информации [см. Office 365 Правительство США DOD конечных точек](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения вы найдете в следующих статьях:

- [Что такое Azure для государственных организаций?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Обновление конечных точек управления Azure Правительства AAD](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph конечные точки в облаке правительства США](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Офис 365 Правительство США ССЗ Высокий и МИНИСТЕРСТВО по делам ИП](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
