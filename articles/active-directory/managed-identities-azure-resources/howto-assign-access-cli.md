---
title: Назначение управляемому удостоверению доступа к ресурсу с помощью Azure CLI Azure AD
description: Пошаговые инструкции по назначению управляемого удостоверения для одного ресурса и доступу к другому ресурсу с помощью Azure CLI.
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
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13c69dda1e300bcff95b6a017fdeb308a6bbf3a4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969259"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Назначение доступа на основе управляемого удостоверения к ресурсу с помощью Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

После настройки ресурса Azure с помощью управляемого удостоверения можно предоставить доступ на основе управляемого удостоверения другому ресурсу, как и любому субъекту безопасности. В этом примере показано, как предоставить виртуальной машине или масштабируемому набору виртуальных машин Azure доступ на основе управляемого удостоверения к учетной записи хранения Azure с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#managed-identity-types)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения примеров сценариев существует два варианта.
    - Используйте [Azure Cloud Shell](../../cloud-shell/overview.md), которую можно открыть с помощью кнопки **попробовать** в верхнем правом углу блоков кода.
    - Выполните скрипты локально, установив последнюю версию [Azure CLI](/cli/azure/install-azure-cli), а затем войдите в Azure с помощью команды [AZ login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, в которой вы хотите создавать ресурсы.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Назначение управляемому удостоверению доступа к другому ресурсу с помощью Azure RBAC

После включения управляемого удостоверения в ресурсе Azure, например на [виртуальной машине Azure](qs-configure-cli-windows-vm.md) или в [масштабируемом наборе виртуальных машин Azure](qs-configure-cli-windows-vmss.md), сделайте следующее: 

1. В этом примере мы предоставляем виртуальной машине Azure доступ к учетной записи хранения. Сначала мы выполним команду [az resource list](/cli/azure/resource/#az-resource-list), чтобы получить субъект-службу для виртуальной машины с именем myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Для масштабируемого набора виртуальных машин Azure используется та же команда. Только в результате ее выполнения вы получите масштабируемый набор виртуальных машин с именем DevTestVMSS:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. После получения идентификатора субъекта-службы выполните команду [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), чтобы предоставить этой виртуальной машине или масштабируемому набору виртуальных машин доступ для чтения к учетной записи хранения myStorageAcct:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор управляемых удостоверений для ресурсов Azure](overview.md).
- См. дополнительные сведения о [настройке управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md).
- См. дополнительные сведения о [настройке управляемых удостоверений для ресурсов Azure в масштабируемом наборе виртуальных машин с помощью Azure CLI](qs-configure-cli-windows-vmss.md).
