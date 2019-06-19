---
title: Новые возможности Azure Active Directory в Microsoft 365 Government — Azure Active Directory | Документация Майкрософт
description: Узнайте о некоторых изменениях в Azure Active Directory (Azure AD) в экземпляр облака Microsoft 365 для государственных организаций, которая может влиять на.
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
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258899"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Новые возможности Azure Active Directory в Microsoft 365 для государственных организаций

Мы внесли некоторые изменения в Azure Active Directory (Azure AD) в экземпляр облака Microsoft 365 для государственных организаций, который предоставляется клиентам, с помощью следующих служб:

- Azure для государственных организаций

- Microsoft 365 для государственных организаций — GCC высокого уровня

- Microsoft 365 для государственных организаций — министерства обороны США

Эта статья не относится к Microsoft 365 Government — GCC клиентов.

## <a name="changes-to-the-initial-domain-name"></a>Изменения в исходное доменное имя

Во время первой вашей организации, зарегистрируйтесь в Microsoft 365 для государственных организаций online Service, было предложено выбрать имя домена вашей организации, `<your-domain-name>.onmicrosoft.com`. Если у вас уже есть доменное имя с суффиксом .com, ничего не изменится.

Тем не менее, если вы входите в для новой службы Microsoft 365 для государственных организаций, вам будет предложено выбрать имя домена с помощью `.us` суффикс. Таким образом, он будет `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Это изменение не относится к любой клиентов, которые управляются поставщиками облачных служб (CSP).

## <a name="changes-to-portal-access"></a>Изменения доступа к порталу

Мы обновили конечные точки на портале Microsoft Azure для государственных организаций, Microsoft 365 для государственных организаций — GCC High и Microsoft 365 Government – министерства обороны США, как показано в [таблицу сопоставления конечной точки](#endpoint-mapping).

Ранее клиентам удалось вход с использованием на порталах Office 365 (portal.office.com) и по всему миру Azure (portal.azure.com). Это обновление клиентов теперь необходимо войти с использованием определенного Microsoft Azure для государственных организаций, Microsoft 365 для государственных организаций — GCC High и Microsoft 365 Government - порталы министерства обороны США.

## <a name="endpoint-mapping"></a>Сопоставление конечных точек

В следующей таблице показаны конечные точки для всех клиентов:

| ИМЯ | сведения о конечной точке |
|------|------------------|
| Порталы |Microsoft Azure для государственных организаций: https://portal.azure.us<p>Microsoft 365 для государственных организаций — GCC высокого уровня: https://portal.office365.us<p>Microsoft 365 для государственных организаций — министерства обороны США: https://portal.apps.mil |
| Конечная точка авторизации Azure Active Directory | https://login.microsoftonline.us |
| API Graph Azure Active Directory | https://graph.windows.net |
| Microsoft Graph API для Microsoft 365 для государственных организаций — GCC высокого уровня | https://graph.microsoft.us |
| Microsoft Graph API для Microsoft 365 для государственных организаций - министерства обороны США | https://dod-graph.microsoft.us |
| Конечные точки служб Azure для государственных организаций | Дополнительные сведения см. в разделе [руководство разработчика Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 для государственных организаций — GCC High конечных точек | Дополнительные сведения см. в разделе [США Office 365 Конечные точки GCC High для государственных организаций](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 для государственных организаций - министерства обороны США | Дополнительные сведения см. в разделе [США Office 365 Конечные точки для государственных организаций министерства обороны США](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения вы найдете в следующих статьях:

- [Что такое Azure для государственных организаций?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Обновление конечной точки центра AAD Azure для государственных организаций](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Конечных точек Microsoft Graph в облаке для государственных организаций США](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 для государственных организаций США GCC High и министерства обороны США](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)