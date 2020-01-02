---
title: Новые возможности Azure AD в Microsoft 365 правительственных учреждениях | Документация Майкрософт
description: Узнайте об изменениях в Azure Active Directory (Azure AD) в облачном экземпляре Microsoft 365, что может повлиять на вас.
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
ms.openlocfilehash: 75e7c6809810a78c81348bd4bbeb808e103f453f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805281"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Новые возможности Azure Active Directory в Microsoft 365 государственных организаций

Мы внесли некоторые изменения в Azure Active Directory (Azure AD) в облачном экземпляре Microsoft 365 для государственных организаций, который применим к клиентам, использующим следующие службы:

- Azure для государственных организаций

- Microsoft 365 правительственные учреждения — GCC High

- Microsoft 365 правительственные учреждения — DoD

Эта статья не относится к Microsoft 365 правительственным учреждениям — клиентам GCC.

## <a name="changes-to-the-initial-domain-name"></a>Изменение исходного доменного имени

Во время начальной регистрации вашей организации для службы Microsoft 365 государственных организаций вам было предложено выбрать доменное имя Организации, `<your-domain-name>.onmicrosoft.com`. Если у вас уже есть имя домена с суффиксом. com, ничего не изменится.

Однако если вы регистрируетесь для использования новой службы Microsoft 365 правительственных учреждений, вам будет предложено выбрать доменное имя с помощью суффикса `.us`. Таким образом, он будет `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Это изменение не относится к клиентам, которые управляются поставщиками облачных служб (CSP).

## <a name="changes-to-portal-access"></a>Изменения в доступе к порталу

Мы обновили конечные точки портала для Microsoft Azure для государственных организаций, Microsoft 365 правительственные учреждения — GCC High и Microsoft 365 правительства — DoD, как показано в [таблице сопоставления конечных точек](#endpoint-mapping).

Ранее клиенты могли входить с помощью порталов Azure (portal.azure.com) и Office 365 (portal.office.com) на разных языках. После этого обновления клиенты должны войти в систему, используя конкретные Microsoft Azure для государственных организаций, Microsoft 365 правительственные учреждения и Microsoft 365ные порталы для государственных организаций.

## <a name="endpoint-mapping"></a>Сопоставление конечных точек

В следующей таблице показаны конечные точки для всех клиентов.

| имя | Сведения о конечной точке |
|------|------------------|
| Порталы |Microsoft Azure для государственных организаций: https://portal.azure.us<p>Microsoft 365 правительственные учреждения — GCC High: https://portal.office365.us<p>Microsoft 365ные учреждения — DoD: https://portal.apps.mil |
| Конечная точка центра Azure Active Directory | https://login.microsoftonline.us |
| API Graph Azure Active Directory | https://graph.windows.net |
| Microsoft Graph API для Microsoft 365 государственных организаций — GCC High | https://graph.microsoft.us |
| Microsoft Graph API для Microsoft 365 государственных организаций — DoD | https://dod-graph.microsoft.us |
| Конечные точки служб Azure для государственных организаций | Дополнительные сведения см. в разделе [руководством для разработчиков Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) . |
| Microsoft 365 государственные учреждения — старшие конечные точки GCC | Дополнительные сведения см. в статье [Office 365 U.S. правительства GCC High Endpoints](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 для государственных организаций — DoD | Дополнительные сведения см. в разделе [конечные точки Microsoft Office 365 США для государственных организаций](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения вы найдете в следующих статьях:

- [Что такое Azure для государственных организаций?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Обновление конечной точки центра сертификации AAD в Azure для государственных организаций](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph конечных точек в облаке правительства США](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 США, американская для государственных организаций, GCC High и DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)