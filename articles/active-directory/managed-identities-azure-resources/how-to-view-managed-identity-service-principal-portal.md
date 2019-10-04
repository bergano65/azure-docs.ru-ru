---
title: Просмотр субъекта-службы управляемого удостоверения на портале Azure
description: Пошаговое руководство по просмотру субъекта-службы управляемого удостоверения на портале Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a8db0254f85980022115cab5d73f7f329b57001
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827552"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Просмотр субъекта-службы управляемого удостоверения на портале Azure

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье описано, как просмотреть субъект-службу управляемого удостоверения на портале Azure.

 > [!NOTE] 
 > Субъекты-службы — это корпоративные приложения. 

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md).
- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) или [приложении](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Просмотр субъекта-службы

Вы можете просмотреть субъект-службу виртуальной машины с включенным управляемым удостоверением, назначаемым системой (те же действия применяются для приложения).

1. Щелкните **Azure Active Directory** и выберите **Корпоративные приложения**.
2. В списке **Тип приложения** выберите **Все приложения**.
3. В поле фильтра поиска введите имя виртуальной машины или приложения с включенным управляемым удостоверением либо воспользуйтесь списком.

   ![Просмотр субъектов-служб для управляемых удостоверений на портале](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Следующие шаги

[Управляемые удостоверения для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview)

