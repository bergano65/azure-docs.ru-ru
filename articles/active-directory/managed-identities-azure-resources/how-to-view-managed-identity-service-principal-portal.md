---
title: Просмотр субъекта-службы управляемого удостоверения в портал Azure Azure AD
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
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79298603"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Просмотр субъекта-службы управляемого удостоверения на портале Azure

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье описано, как просмотреть субъект-службу управляемого удостоверения на портале Azure.

 > [!NOTE] 
 > Субъекты-службы — это корпоративные приложения. 

## <a name="prerequisites"></a>Предварительные условия

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md).
- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) или [приложении](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Просмотр субъекта-службы

Вы можете просмотреть субъект-службу виртуальной машины с включенным управляемым удостоверением, назначаемым системой (те же действия применяются для приложения).

1. Щелкните **Azure Active Directory** и выберите **Корпоративные приложения**.
2. В разделе **Тип приложения**выберите **все приложения** , а затем нажмите кнопку **Применить**.
3. В поле фильтра поиска введите имя виртуальной машины или приложения с включенным управляемым удостоверением либо воспользуйтесь списком.

   ![Просмотр субъектов-служб для управляемых удостоверений на портале](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Дальнейшие шаги

[Управляемые удостоверения для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview)

