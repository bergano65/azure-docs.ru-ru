---
title: Просмотр субъекта-службы управляемого удостоверения с помощью Azure CLI
description: Пошаговое руководство по просмотру субъекта-службы управляемого удостоверения с помощью Azure CLI.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: priyamo
ms.openlocfilehash: 8aadb8f0f8b3de9aab37689cc80cea211ad01165
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162282"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Просмотр субъекта-службы управляемого удостоверения с помощью Azure CLI

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня учетные данные в коде. 

В этой статье описано, как просмотреть субъект-службу управляемого удостоверения с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md).
- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) или [приложении](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:
    - использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
    - использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
    - [установить последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), если вы предпочитаете использовать локальную консоль CLI, и войти в Azure с помощью `az login`.
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Просмотр субъекта-службы

Следующая команда позволяет просмотреть субъект-службу виртуальной машины или приложения с включенным управляемым удостоверением. Замените `<VM or application name>` собственными значениями. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управлении субъектами-службами Azure AD с помощью Azure CLI см. в разделе [az ad sp](/cli/azure/ad/sp).


