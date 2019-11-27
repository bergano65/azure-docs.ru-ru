---
title: Управление назначенными пользователем управляемыми удостоверениями с помощью функции RESTFUL в Azure AD
description: Пошаговые инструкции по созданию и удалению управляемых удостоверений, назначаемых пользователем, а также получению их списка для выполнения вызовов REST API.
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
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547415"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью вызовов REST API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемые удостоверения для ресурсов Azure можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без указания учетных данных в коде. 

В этой статье вы узнаете, как создавать и удалять управляемые удостоверения, назначаемые пользователем, а также получать их список с помощью CURL для выполнения вызовов REST API.

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Если вы используете Windows, установите [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или используйте [Azure Cloud Shell](../../cloud-shell/overview.md) на портале Azure.
- Если вы используете [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или [дистрибутив Linux OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [установите локальную консоль Azure CLI](/cli/azure/install-azure-cli).
- Если вы используете локальную консоль Azure CLI, войдите в Azure с помощью `az login` с учетной записью, привязанной к подписке Azure, в которой вы хотите развернуть управляемое удостоверение, назначаемое пользователем, или получить его.
- Извлечение маркера доступа носителя с помощью `az account get-access-token` для выполнения следующих операций с управляемыми удостоверениями, назначаемыми пользователем.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Создание назначаемого пользователем управляемого удостоверения. 

Чтобы создать назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [участника управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Заголовки запроса**

|Заголовок запроса  |ОПИСАНИЕ  |
|---------|---------|
|*Content-Type*     | обязательный параметр. Задайте значение `application/json`.        |
|*Авторизация*     | обязательный параметр. Задайте допустимый маркер доступа для `Bearer`.        |

**Текст запроса**

|имя  |ОПИСАНИЕ  |
|---------|---------|
|location     | обязательный параметр. Расположение ресурса.        |

## <a name="list-user-assigned-managed-identities"></a>Получение списка управляемых удостоверений, назначаемых пользователем

Чтобы получить список управляемых удостоверений, назначаемых пользователем, или отобразить их, учетной записи должна быть назначена роль [оператора управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) или [участника управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Заголовок запроса  |ОПИСАНИЕ  |
|---------|---------|
|*Content-Type*     | обязательный параметр. Задайте значение `application/json`.        |
|*Авторизация*     | обязательный параметр. Задайте допустимый маркер доступа для `Bearer`.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Удаление управляемого удостоверения, назначаемого пользователем

Чтобы удалить назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [Участника управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

> [!NOTE]
> При удалении управляемого удостоверения, назначаемого пользователем, не удаляются ссылки из ресурсов, которым оно было назначено. Чтобы удалить управляемое удостоверение, назначаемое пользователем, из виртуальной машины с помощью CURL, обратитесь к разделу [Удаление назначенного пользователем удостоверения из виртуальной машины Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Заголовок запроса  |ОПИСАНИЕ  |
|---------|---------|
|*Content-Type*     | обязательный параметр. Задайте значение `application/json`.        |
|*Авторизация*     | обязательный параметр. Задайте допустимый маркер доступа для `Bearer`.        |

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины или масштабируемого набора виртуальных машин Azure, см. в разделах [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью вызовов REST API](qs-configure-rest-vm.md#user-assigned-managed-identity) и [Настройка управляемых удостоверений для ресурсов Azure в масштабируемом наборе виртуальных машин с помощью вызовов REST API](qs-configure-rest-vmss.md#user-assigned-managed-identity).
