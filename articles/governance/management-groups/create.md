---
title: Создание групп управления для упорядочения ресурсов Azure. Система управления Azure
description: Узнайте, как создавать группы управления Azure для управления множеством ресурсов с помощью портала, Azure PowerShell и Azure CLI.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 2dd2a6e071533deef47a6482bfb9ed92953864ba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259820"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Создание групп управления для упорядочения ресурсов и управления ими

Группы управления — это контейнеры, которые помогают управлять доступом, политикой и соответствием требованиям в нескольких подписках. Создание таких контейнеров позволяет построить эффективную и экономную иерархию, которую можно использовать с [политикой Azure](../policy/overview.md) и [элементами управления доступом на основе ролей Azure](../../role-based-access-control/overview.md). Дополнительные сведения о группах управления см. в статье [Упорядочение ресурсов с помощью групп управления Azure](overview.md).

Создание первой группы управления в каталоге может занять до 15 минут. Существуют процессы, выполняемые в первый раз при настройке службы групп управления в Azure для вашего каталога. По завершении процесса вы получите уведомление.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>Создание группы управления

Группу управления можно создать с помощью портала, PowerShell или Azure CLI. В настоящее время нельзя использовать шаблоны Resource Manager для создания групп управления.

### <a name="create-in-portal"></a>Создание на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. На главной странице выберите **Создать группу управления**.

   ![Страница для работы с группами управления](./media/main.png)

1. Заполните поле идентификатора группы управления.

   - **Идентификатор группы управления** — уникальный идентификатор каталога, используемый для отправки команд в этой группе управления. Идентификатор не редактируется после ее создания, так как с его помощью эта группа идентифицируется во всей системе Azure. [Корневую группу управления](index.md#root-management-group-for-each-directory) создается автоматически с Идентификатором, который является идентификатором Azure Active Directory. Для всех других групп управления назначьте уникальный идентификатор.
   - Поле отображаемого имени — это имя, которое отображается во всех разделах портала Azure. Отдельное отображаемое имя можно указать в необязательном поле при создании группы управления и изменить в любой момент.  

   ![Панель параметров для создания новой группы управления](./media/create_context_menu.png)  

1. Щелкните **Сохранить**.

### <a name="create-in-powershell"></a>Создание в PowerShell

С помощью PowerShell, используйте [New AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) командлет, чтобы создать новую группу управления.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** — уникальный создаваемый идентификатор. Этот идентификатор используется в других командах для ссылки на эту группу, и его нельзя будет изменить позже.

Если требуется использовать другое имя на портале Azure в группу управления, добавьте **DisplayName** параметра. Например чтобы создать группу управления с GroupName компании Contoso и отображаемое имя «Группа Contoso», используйте следующий командлет:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

В приведенных выше примерах новой группы управления создается в корневую группу управления. Чтобы указать другой группе управления в качестве родительского элемента, используйте **ParentId** параметра.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Создание в Azure CLI

С помощью Azure CLI, используйте [создания группы управления в учетной записи az](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) команду, чтобы создать новую группу управления.

```azurecli-interactive
az account management-group create --name Contoso
```

**Имя** — это уникальный идентификатор, который создается. Этот идентификатор используется в других командах для ссылки на эту группу, и его нельзя будет изменить позже.

Если требуется использовать другое имя на портале Azure в группу управления, добавьте **отображаемое имя** параметра. Например чтобы создать группу управления с GroupName компании Contoso и отображаемое имя «Группа Contoso», используйте следующую команду:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

В приведенных выше примерах новой группы управления создается в корневую группу управления. Чтобы указать другой группе управления в качестве родительского элемента, используйте **родительского** параметр и укажите имя родительской группы.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о группах управления:

- [Создание групп управления для организации ресурсов Azure](create.md)
- [Как изменить, удалить или управлять группами управления](manage.md)
- [Просмотр групп управления в модуле ресурсов Azure PowerShell](/powershell/module/az.resources#resources)
- [Просмотр групп управления в REST API](/rest/api/resources/managementgroups)
- [Просмотр групп управления в Azure CLI](/cli/azure/account/management-group)