---
title: Просмотр субъекта-службы управляемого удостоверения с помощью Azure CLI
description: Пошаговое руководство по просмотру субъекта-службы управляемого удостоверения с помощью Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: c1e09ac91055cc02fa0ab5c2f4576d6ed6a6d3e7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885902"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Просмотр субъекта-службы управляемого удостоверения с помощью Azure CLI

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня учетные данные в коде. 

В этой статье описано, как просмотреть субъект-службу управляемого удостоверения с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md).
- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) или [приложении](/azure/app-service/app-service-managed-service-identity#adding-a-system-assigned-identity).
- Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:
    - использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел);
    - использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода;
    - [установить последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), если вы предпочитаете использовать локальную консоль CLI, и войти в Azure с помощью `az login`.
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Просмотр субъекта-службы

Следующая команда позволяет просмотреть субъект-службу виртуальной машины или приложения с включенным управляемым удостоверением. Замените `<VM or application name>` собственными значениями. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управлении субъектами-службами Azure AD с помощью Azure CLI см. в разделе [az ad sp](/cli/azure/ad/sp).


