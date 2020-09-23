---
title: Управление назначенными пользователем управляемым удостоверением — Azure CLI Azure AD
description: Пошаговые инструкции по созданию, отчислению и удалению назначенного пользователем управляемого удостоверения с помощью Azure CLI.
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
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a1a991ab79c38dad1a89533091d80406615d35
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969475"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью Azure CLI


Управляемые удостоверения для ресурсов Azure предоставляют службы Azure с управляемым удостоверением в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

Из этой статьи вы узнаете, как создать, перечислить и удалить назначенное пользователем управляемое удостоверение с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#managed-identity-types)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения примеров сценариев существует два варианта.
    - Используйте [Azure Cloud Shell](../../cloud-shell/overview.md), которую можно открыть с помощью кнопки **попробовать** в верхнем правом углу блоков кода.
    - Выполните скрипты локально, установив последнюю версию [Azure CLI](/cli/azure/install-azure-cli), а затем войдите в Azure с помощью команды [AZ login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, в которой вы хотите создавать ресурсы.   

> [!NOTE]
> Чтобы изменить разрешения пользователя при использовании субъекта-службы приложений с помощью интерфейса командной строки, необходимо предоставить дополнительные разрешения субъекта-службы в Azure AD API Graph как части CLI выполняют запросы GET к API Graph. В противном случае вы можете получить сообщение "недостаточно прав для завершения операции". Для этого необходимо перейти в раздел Регистрация приложения в Azure Active Directory, выбрать приложение, щелкнуть разрешения API, прокрутить вниз и выбрать Azure Active Directory граф. В окне выберите разрешения приложения, а затем добавьте соответствующие разрешения. 

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем 

Чтобы создать назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [участника управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Используйте команду [az identity create](/cli/azure/identity#az-identity-create), чтобы создать управляемое удостоверение, назначаемое пользователем. Параметр `-g` указывает группу ресурсов, в которой создано управляемое удостоверение, назначаемое пользователем, а параметр `-n` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Получение списка управляемых удостоверений, назначаемых пользователем

Чтобы получить список управляемых удостоверений, назначаемых пользователем, или отобразить их, учетной записи должна быть назначена роль [оператора управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-operator) или [участника управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Для получения списка управляемых удостоверений, назначаемых пользователем, используйте команду [az identity list](/cli/azure/identity#az-identity-list). Замените `<RESOURCE GROUP>` собственным значением.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

В ответе JSON управляемые удостоверения, назначаемые пользователем, содержат значение `"Microsoft.ManagedIdentity/userAssignedIdentities"`, возвращаемое для ключа `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Удаление управляемого удостоверения, назначаемого пользователем

Чтобы удалить назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [участника управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Для удаления управляемого удостоверения, назначаемого пользователем, используйте команду [az identity delete](/cli/azure/identity#az-identity-delete).  Параметр -n указывает имя, а параметр -g — группу ресурсов, в которой было создано управляемое удостоверение, назначаемое пользователем. Замените значения параметров `<USER ASSIGNED IDENTITY NAME>` и `<RESOURCE GROUP>` собственными:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> При удалении управляемого удостоверения, назначаемого пользователем, не удаляются ссылки из ресурсов, которым оно было назначено. Удалите их из виртуальной машины или VMSS с помощью команды `az vm/vmss identity remove`.

## <a name="next-steps"></a>Дальнейшие действия

Полный список команд Azure CLI, используемых для работы с удостоверениями, см. в разделе [az identity](/cli/azure/identity).

Сведения о том, как задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины Azure, см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity).
