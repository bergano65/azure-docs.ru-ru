---
title: Защита зон и записей DNS - Azure DNS
description: На этом пути обучения начинайте защищать зоны DNS и записи в Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 89a945f146601084795b2e12a721a03a1b96aaea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371483"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Как защитить зоны и записи DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Зоны и записи DNS являются критически важными ресурсами. Удалять зону DNS или одну запись DNS может привести к сбою в обслуживании. Важно, чтобы зоны и записи DNS были защищены от несанкционированных или случайных изменений.

В этой статье объясняется, как Azure DNS позволяет защитить ваши частные Зоны DNS и записи от таких изменений.  Мы применяем две мощные функции ценных бумаг, предоставляемые менеджером ресурсов Azure: [управление доступом на основе ролей](../role-based-access-control/overview.md) и [блокировки ресурсов.](../azure-resource-manager/management/lock-resources.md)

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Управление доступом на основе ролей (RBAC) Azure обеспечивает точное управление доступом для пользователей, групп и ресурсов Azure. С помощью RBAC можно предоставить необходимый пользователям уровень доступа. Дополнительные сведения о том, как RBAC помогает управлять доступом, см. в статье [Начало работы с управлением доступом на портале Azure](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Роль "Участник зоны DNS"

Роль вкладчика DNS-зоны является встроенной ролью для управления частными ресурсами DNS. Эта роль, применяемая к пользователю или группе, позволяет им управлять ресурсами DNS.

Ресурсная группа *myResourceGroup* содержит пять зон для корпорации Contoso. Если предоставить администратору DNS разрешения "Участник зоны DNS" для этой группы ресурсов, то он получит полный контроль над этими зонами DNS. Это позволяет избежать выдачи ненужных разрешений. Администратор DNS не может создавать или останавливать виртуальные машины.

Самый простой способ назначения разрешений RBAC — [через портал Azure](../role-based-access-control/role-assignments-portal.md).  

Открытый **контроль доступа (IAM)** для группы ресурсов, затем выберите **Добавить,** а затем выберите роль **вкладчика DNS зоны.** Выберите необходимых пользователей или групп для предоставления разрешений.

![Назначение разрешений RBAC на уровне группы ресурсов через портал Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Разрешения также можно [предоставить с помощью Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Аналогичную команду также [можно выполнить через интерфейс командной строки Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC на уровне зоны

Правила RBAC Azure могут применяться к подписке, группе ресурсов или к отдельному ресурсу. Этот ресурс может быть отдельной зоной DNS или индивидуальным набором записей.

Например, ресурсная группа *myResourceGroup* содержит зону *contoso.com* и подзону *customers.contoso.com.* Для каждой учетной записи клиента создаются записи CNAME. Учетная запись администратора, используемая для управления записями CNAME, присваивается разрешения на создание записей в *customers.contoso.com* зоне. Учетная запись может управлять только *customers.contoso.com.*

Разрешения RBAC на уровне зоны можно предоставлять через портал Azure.  Открытый **контроль доступа (IAM)** для зоны, выберите **Добавить,** затем выберите роль **вкладчика DNS зоны** и выберите необходимых пользователей или групп для предоставления разрешений.

![Назначение разрешений RBAC на уровне зоны DNS через портал Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Разрешения также можно [предоставить с помощью Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Аналогичную команду также [можно выполнить через интерфейс командной строки Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>RBAC на уровне набора записей

Разрешения применяются на уровне рекордов.  Пользователю предоставляется контроль над нужными им записи и он не может вносить какие-либо другие изменения.

Разрешения RBAC уровня записи можно настроить через портал Azure, используя кнопку **Управления доступом (IAM)** на странице набора записей:

![Назначение разрешений RBAC на уровне набора записей через портал Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Разрешения RBAC на уровне набора записей также можно [предоставить с помощью Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Аналогичную команду также [можно выполнить через интерфейс командной строки Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Пользовательские роли

Встроенная роль "Участник зоны DNS" разрешает полный контроль над ресурсом DNS. Можно создать собственные пользовательские роли Azure, чтобы обеспечить более тонкий контроль.

Учетная запись, используемая для управления CNAMEs, получает разрешение на управление только записями CNAME. Учетная запись не может изменять записи других типов. Учетная запись не может выполнять операции на уровне зоны, такие как удаление зоны.

В следующем примере показано определение пользовательской роли для управления только записями CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
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

* `Microsoft.Network/dnsZones/CNAME/*` предоставляет полный контроль над записями CNAME.
* `Microsoft.Network/dnsZones/read` предоставляет разрешение на чтение зон DNS, но не позволяет их изменять. Таким образом вы можете видеть зону, в которой создается запись CNAME.

Остальные элементы свойства Actions копируются из [встроенной роли участника зоны DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Применение пользовательской роли RBAC для предотвращения удаления наборов записей, разрешая при этом их обновление, не является эффективным. Наборы записей нельзя удалить, но ничто не препятствует их изменению.  К разрешенным изменениям относятся добавление и удаление записей из набора записей, включая удаление всех записей (при этом остается "пустой" набор записей). Это действует так же, как удаление набора записей с точки зрения разрешения DNS.

Пользовательские определения ролей в настоящее время не могут быть определены через портал Azure. Пользовательскую роль на основе этого определения роли можно создать с помощью Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Ее также можно создать с помощью интерфейса командной строки Azure:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

Затем роль можно назначить таким же образом, как и встроенные роли. Этот процесс описан ранее в этой статье.

Дополнительные сведения о создании и назначении пользовательских ролей, а также об управлении ими см. в статье [Пользовательские роли в Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Блокировки ресурсов

Диспетчер ресурсов Azure поддерживает другой тип управления безопасностью — возможность блокировки ресурсов. Блокировки ресурсов применяются к ресурсу и эффективны для всех пользователей и ролей. Для получения дополнительной информации смотрите [ресурсы блокировки с менеджером ресурсов Azure.](../azure-resource-manager/management/lock-resources.md)

Есть два типа блокировки ресурсов: **CanNotDelete** и **ReadOnly**. Эти типы блокировки могут быть применены либо к частной зоне DNS, либо к отдельному набору записей. Следующие разделы описывают несколько распространенных сценариев и способы их поддержки с помощью блокировок ресурсов.

### <a name="protecting-against-all-changes"></a>Защита от любых изменений

Чтобы предотвратить внесение изменений, примените блокировку ReadOnly в зону. Эта блокировка предотвращает создание новых наборов записей, а существующие наборы записей изменяются или удаляются.

Блокировки ресурсов на уровне зоны можно создавать через портал Azure.  На странице зоны DNS выберите **Блокировки**, затем щелкните **+ Добавить**:

![Блокировка ресурсов на уровне зоны через портал Azure](./media/dns-protect-zones-recordsets/locks1.png)

Блокировки ресурсов уровня зоны также могут быть созданы через [Azure PowerShell:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Аналогичную команду также [можно выполнить через интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create):

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Защита отдельных записей

Чтобы предотвратить внесение изменений в существующий набор записей DNS, примените к нему блокировку ReadOnly.

> [!NOTE]
> Применение к набору записей блокировки CanNotDelete (Запрет удаления) не является эффективным. Набор записей нельзя удалить, но ничто не препятствует его изменению.  К разрешенным изменениям относятся добавление и удаление записей из набора записей, включая удаление всех записей (при этом остается "пустой" набор записей). Это действует так же, как удаление набора записей с точки зрения разрешения DNS.

В настоящее время блокировки ресурсов на уровне наборов записей можно настраивать только с помощью Azure PowerShell.  Они не поддерживаются на портале Azure или Azure CLI.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Защита зоны от удаления

При удалении зоны в Azure DNS все наборы записей в зоне удаляются.  Эта операция не может быть отменена. Случайное удаление критически важной зоны может оказать серьезное влияние на коммерческую деятельность.  Важно защититься от случайного удаления зон.

Применение блокировки CanNotDelete (Запрет удаления) предотвращает удаление зоны. Замки наследуются детскими ресурсами. Блокировка предотвращает удаление любых записей, устанавливаемых в зоне. Как описано в примечании выше, это неэффективно, так как записи все еще могут быть удалены из существующих наборов записей.

В качестве альтернативы примените блокировку CanNotDelete к рекорду, установленного в зоне, например, к набору записей SOA. Зона не удаляется без удаления наборов записей. Этот замок защищает от удаления зоны, в то же время позволяя свободно модифицировать наборы записей в зоне. При попытке удалить зону менеджер ресурсов Azure обнаруживает это удаление. Удаление также удалит набор записей SOA, менеджер ресурсов Azure блокирует вызов, поскольку SOA заблокирован.  Наборы записей не будут удалены.

Следующая команда PowerShell создает блокировку CanNotDelete (Запрет удаления) для записи типа SOA в указанной зоне:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
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
