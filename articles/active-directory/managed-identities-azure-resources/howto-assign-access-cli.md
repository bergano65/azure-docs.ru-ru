---
title: Назначение доступа на основе управляемого удостоверения к ресурсу Azure с помощью Azure CLI
description: Пошаговые инструкции по назначению управляемого удостоверения для одного ресурса и предоставлению доступа к другому ресурсу с помощью Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: daveba
ms.openlocfilehash: ffed9e388b41b6c19ae8ed22b9c843f5ac8ceb3e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430421"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Назначение доступа на основе управляемого удостоверения к ресурсу с помощью Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

После настройки управляемого удостоверения для ресурса Azure можно предоставить доступ на основе управляемого удостоверения другому ресурсу, как и любому субъекту безопасности. В этом примере показано, как предоставить виртуальной машине или масштабируемому набору виртуальных машин Azure доступ на основе управляемого удостоверения к учетной записи хранения Azure с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:
    - использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
    - использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
    - Если вы предпочитаете использовать локальную консоль CLI, [установите последнюю версию интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Назначение управляемому удостоверению прав доступа к другому ресурсу Azure с помощью RBAC

После включения управляемого удостоверения в ресурсе Azure, например на [виртуальной машине Azure](qs-configure-cli-windows-vm.md) или в [масштабируемом наборе виртуальных машин Azure](qs-configure-cli-windows-vmss.md), сделайте следующее: 

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, с помощью которой нужно развернуть виртуальную машину или масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. В этом примере мы предоставляем виртуальной машине Azure доступ к учетной записи хранения. Сначала мы выполним команду [az resource list](/cli/azure/resource/#az-resource-list), чтобы получить субъект-службу для виртуальной машины с именем myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Для масштабируемого набора виртуальных машин Azure используется та же команда. Только в результате ее выполнения вы получите масштабируемый набор виртуальных машин с именем DevTestVMSS:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. После получения идентификатора субъекта-службы выполните команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), чтобы предоставить этой виртуальной машине или масштабируемому набору виртуальных машин доступ для чтения к учетной записи хранения myStorageAcct:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Дополнительная информация

- [Обзор управляемых удостоверений для ресурсов Azure](overview.md).
- См. дополнительные сведения о [настройке управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md).
- См. дополнительные сведения о [настройке управляемых удостоверений для ресурсов Azure в масштабируемом наборе виртуальных машин с помощью Azure CLI](qs-configure-cli-windows-vmss.md).