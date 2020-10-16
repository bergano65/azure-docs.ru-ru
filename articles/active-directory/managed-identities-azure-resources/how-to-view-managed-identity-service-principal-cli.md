---
title: Просмотр субъекта-службы управляемого удостоверения — Azure CLI Azure AD
description: Пошаговое руководство по просмотру субъекта-службы управляемого удостоверения с помощью Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c0f167a36ceff6a9b62e2a3ccf24ca6e13e6294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611917"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Просмотр субъекта-службы управляемого удостоверения с помощью Azure CLI

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня учетные данные в коде. 

В этой статье описано, как просмотреть субъект-службу управляемого удостоверения с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md).
- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- [Включите назначенное системой удостоверение в виртуальной машине](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) или [приложении](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Выполнить примеры скриптов можно двумя способами:
    - используйте службу [Azure Cloud Shell](../../cloud-shell/overview.md), которую можно открыть с помощью кнопки **Попробовать** в правом верхнем углу блоков кода.
    - Выполните скрипты локально, установив последнюю версию [Azure CLI](/cli/azure/install-azure-cli), а затем войдите в Azure с помощью команды [az login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, в рамках которой нужно создать ресурсы.   

## <a name="view-the-service-principal"></a>Просмотр субъекта-службы

Следующая команда позволяет просмотреть субъект-службу виртуальной машины или приложения с включенным управляемым удостоверением. Замените `<Azure resource name>` собственными значениями.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения об управлении субъектами-службами Azure AD с помощью Azure CLI см. в разделе [az ad sp](/cli/azure/ad/sp).
