---
title: Защита частных Зон и записей DNS - Azure DNS
description: На этом пути обучения начинайте защищать частные DNS-зоны и записи в Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473068"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Как защитить частные зоны и записи DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Частные зоны и записи DNS являются критически важными ресурсами. Удалять зону DNS или одну запись DNS может привести к сбою в обслуживании. Важно, чтобы зоны и записи DNS были защищены от несанкционированных или случайных изменений.

В этой статье объясняется, как Azure DNS позволяет защитить ваши частные Зоны DNS и записи от таких изменений.  Мы применяем две мощные функции ценных бумаг, предоставляемые менеджером ресурсов Azure: [управление доступом на основе ролей](../role-based-access-control/overview.md) и [блокировки ресурсов.](../azure-resource-manager/management/lock-resources.md)

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Управление доступом на основе ролей (RBAC) Azure обеспечивает точное управление доступом для пользователей, групп и ресурсов Azure. С помощью RBAC можно предоставить необходимый пользователям уровень доступа. Дополнительные сведения о том, как RBAC помогает управлять доступом, см. в статье [Начало работы с управлением доступом на портале Azure](../role-based-access-control/overview.md).

### <a name="the-private-dns-zone-contributor-role"></a>Роль вкладчика частной зоны DNS

Роль вкладчика Private DNS-зоны является встроенной ролью для управления частными ресурсами DNS. Эта роль, применяемая к пользователю или группе, позволяет им управлять частными dNS-ресурсами.

Ресурсная группа *myPrivateDNS* содержит пять зон для корпорации Contoso. Предоставление администратору DNS разрешений на доступ к группе ресурсов Private DNS-группы позволяет полностью контролировать эти зоны DNS. Это позволяет избежать выдачи ненужных разрешений. Администратор DNS не может создавать или останавливать виртуальные машины.

Самый простой способ назначения разрешений RBAC — [через портал Azure](../role-based-access-control/role-assignments-portal.md).  

Открытый **контроль доступа (IAM)** для группы ресурсов, выберите **Добавить,** а затем выберите роль **частного dNS-зоны.** Выберите необходимых пользователей или групп для предоставления разрешений.

![Назначение разрешений RBAC на уровне группы ресурсов через портал Azure](./media/dns-protect-private-zones-recordsets/rbac1.png)

Разрешения также можно [предоставить с помощью Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Аналогичную команду также [можно выполнить через интерфейс командной строки Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>Частная зона уровня RBAC

Правила RBAC Azure могут применяться к подписке, группе ресурсов или к отдельному ресурсу. Этот ресурс может быть отдельной зоной DNS или индивидуальным набором записей.

Например, ресурсная группа *myPrivateDNS* содержит зону *private.contoso.com* и подзону *customers.private.contoso.com.* Для каждой учетной записи клиента создаются записи CNAME. Учетная запись администратора, используемая для управления записями CNAME, присваивается разрешения на создание записей в *customers.private.contoso.com* зоне. Учетная запись может управлять только *customers.private.contoso.com.*

Разрешения RBAC на уровне зоны можно предоставлять через портал Azure.  Открытый **контроль доступа (IAM)** для зоны, выберите **Добавить,** а затем выберите **роль частного dNS Зоны.** Выберите необходимых пользователей или групп для предоставления разрешений.

![Назначение разрешений RBAC на уровне зоны DNS через портал Azure](./media/dns-protect-private-zones-recordsets/rbac2.png)

Разрешения также можно [предоставить с помощью Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Аналогичную команду также [можно выполнить через интерфейс командной строки Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>RBAC на уровне набора записей

Разрешения применяются на уровне рекордов.  Пользователю предоставляется контроль над нужными им записи и он не может вносить какие-либо другие изменения.

Разрешения RBAC уровня записи можно настроить через портал Azure, используя кнопку **Управления доступом (IAM)** на странице набора записей:

![Назначение разрешений RBAC на уровне набора записей через портал Azure](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Назначение разрешений RBAC на уровне набора записей через портал Azure](./media/dns-protect-private-zones-recordsets/rbac4.png)

Разрешения RBAC на уровне набора записей также можно [предоставить с помощью Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Аналогичную команду также [можно выполнить через интерфейс командной строки Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Пользовательские роли

Встроенная роль private DNS-зоны позволяет полностью контролировать ресурс DNS. Можно создать собственные пользовательские роли Azure, чтобы обеспечить более тонкий контроль.

Учетная запись, используемая для управления CNAMEs, получает разрешение на управление только записями CNAME. Учетная запись не может изменять записи других типов. Учетная запись не может выполнять операции на уровне зоны, такие как удаление зоны.

В следующем примере показано определение пользовательской роли для управления только записями CNAME:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Свойство Actions определяет следующие разрешения для DNS:

* `Microsoft.Network/privateDnsZones/CNAME/*` предоставляет полный контроль над записями CNAME.
* `Microsoft.Network/privateDNSZones/read`предоставляет разрешение на чтение частных зон DNS, но не изменять их, что позволяет видеть зону, в которой создается CNAME.

> [!NOTE]
> Применение пользовательской роли RBAC для предотвращения удаления наборов записей, разрешая при этом их обновление, не является эффективным. Наборы записей нельзя удалить, но ничто не препятствует их изменению.  К разрешенным изменениям относятся добавление и удаление записей из набора записей, включая удаление всех записей (при этом остается "пустой" набор записей). Это действует так же, как удаление набора записей с точки зрения разрешения DNS.

Пользовательские определения ролей в настоящее время не могут быть определены через портал Azure. Пользовательскую роль на основе этого определения роли можно создать с помощью Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Ее также можно создать с помощью интерфейса командной строки Azure:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

Затем роль можно назначить таким же образом, как и встроенные роли. Этот процесс описан ранее в этой статье.

Дополнительные сведения о создании и назначении пользовательских ролей, а также об управлении ими см. в статье [Пользовательские роли в Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Блокировки ресурсов

Диспетчер ресурсов Azure поддерживает другой тип управления безопасностью — возможность блокировки ресурсов. Блокировки ресурсов применяются к ресурсу и эффективны для всех пользователей и ролей. Для получения дополнительной информации смотрите [ресурсы блокировки с менеджером ресурсов Azure.](../azure-resource-manager/management/lock-resources.md)

Есть два типа блокировки ресурсов: **CanNotDelete** и **ReadOnly**. Эти типы блокировки могут быть применены либо к частной зоне DNS, либо к отдельному набору записей.  Следующие разделы описывают несколько распространенных сценариев и способы их поддержки с помощью блокировок ресурсов.

### <a name="protecting-against-all-changes"></a>Защита от любых изменений

Чтобы предотвратить внесение изменений, примените блокировку ReadOnly в зону. Эта блокировка предотвращает создание новых наборов записей, а существующие наборы записей изменяются или удаляются.

Блокировки ресурсов на уровне зоны можно создавать через портал Azure.  На странице зоны DNS выберите **Блокировки**, затем щелкните **+ Добавить**:

![Блокировка ресурсов на уровне зоны через портал Azure](./media/dns-protect-private-zones-recordsets/locks1.png)

Блокировки ресурсов уровня зоны также могут быть созданы через [Azure PowerShell:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Аналогичную команду также [можно выполнить через интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create):

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Защита отдельных записей

Чтобы предотвратить внесение изменений в существующий набор записей DNS, примените к нему блокировку ReadOnly.

> [!NOTE]
> Применение к набору записей блокировки CanNotDelete (Запрет удаления) не является эффективным. Набор записей нельзя удалить, но ничто не препятствует его изменению.  К разрешенным изменениям относятся добавление и удаление записей из набора записей, включая удаление всех записей (при этом остается "пустой" набор записей). Это действует так же, как удаление набора записей с точки зрения разрешения DNS.

В настоящее время блокировки ресурсов на уровне наборов записей можно настраивать только с помощью Azure PowerShell.  Они не поддерживаются на портале Azure или Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Защита зоны от удаления

При удалении зоны в Azure DNS все наборы записей в зоне удаляются.  Эта операция не может быть отменена. Случайное удаление критически важной зоны может оказать серьезное влияние на коммерческую деятельность.  Важно защититься от случайного удаления зон.

Применение блокировки CanNotDelete (Запрет удаления) предотвращает удаление зоны. Замки наследуются детскими ресурсами. Блокировка предотвращает удаление любых записей, устанавливаемых в зоне. Как описано в примечании выше, это неэффективно, так как записи все еще могут быть удалены из существующих наборов записей.

В качестве альтернативы примените блокировку CanNotDelete к рекорду, установленного в зоне, например, к набору записей SOA. Зона не удаляется без удаления наборов записей. Этот замок защищает от удаления зоны, в то же время позволяя свободно модифицировать наборы записей в зоне. При попытке удалить зону менеджер ресурсов Azure обнаруживает это удаление. Удаление также удалит набор записей SOA, менеджер ресурсов Azure блокирует вызов, поскольку SOA заблокирован.  Наборы записей не будут удалены.

Следующая команда PowerShell создает блокировку CanNotDelete (Запрет удаления) для записи типа SOA в указанной зоне:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Другим вариантом предотвращения случайного удаления зоны является использование пользовательской роли. Эта роль гарантирует, что учетные записи, используемые для управления зонами, не имеют разрешений на удаление зоны. 

При удалении зоны можно принудительно удалить двухэтапное удаление:

 - Во-первых, разрешения на удаление зоны грантов
 - Во-вторых, предоставить разрешения на удаление зоны.

Пользовательская роль работает для всех зон, к которым имеют доступ эти учетные записи. Учетные записи с разрешениями на удаление зоны, такие как владелец подписки, все еще могут случайно удалить зону.

В качестве глубокого подхода к защите зоны DNS можно использовать оба подхода - блокировок ресурсов и пользовательские роли.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о работе с RBAC см. в статье [Начало работы с управлением доступом на портале Azure](../role-based-access-control/overview.md).
* Дополнительные сведения о работе с блокировками ресурсов см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](../azure-resource-manager/management/lock-resources.md).
