---
title: Управление репликами чтения — Azure PowerShell — база данных Azure для PostgreSQL
description: Узнайте, как настраивать реплики чтения и управлять ими в базе данных Azure для PostgreSQL с помощью PowerShell.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 45876c8e176a4f81abfd927b0e2a8df8fc26ac25
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106906"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Создание реплик чтения и управление ими в базе данных Azure для PostgreSQL с помощью PowerShell

Из этой статьи вы узнаете, как создавать реплики чтения и управлять ими в службе "база данных Azure для PostgreSQL" с помощью PowerShell. Дополнительные сведения о репликах чтения см. в [этой статье](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Вы можете создавать реплики чтения и управлять ими с помощью PowerShell.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

- [Модуль AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) , установленный локально или [Azure Cloud Shell](https://shell.azure.com/) в браузере
- [сервер базы данных Azure для PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md);

> [!IMPORTANT]
> Так как модуль Az.PostgreSql PowerShell предоставляется в режиме предварительной версии, его нужно установить отдельно от модуля Az с помощью команды `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Как только модуль Az.PostgreSql PowerShell станет общедоступным, он будет включен в один из будущих выпусков Az PowerShell и встроен в Azure Cloud Shell.

Если вы решили использовать PowerShell локально, подключитесь к учетной записи Azure с помощью командлета [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Функция чтения реплики доступна только для серверов базы данных Azure для PostgreSQL в общего назначения или ценовой категории, оптимизированные для памяти. Убедитесь, что главный сервер находится в одной из этих ценовых категорий.

### <a name="create-a-read-replica"></a>Создание реплики чтения

Чтобы создать сервер реплики чтения, выполните следующую команду:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Для команды `New-AzPostgreSqlServerReplica` обязательны указанные ниже параметры.

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Группа ресурсов, в которой создается сервер реплики.  |
| Имя | mydemoreplicaserver | Имя нового сервера реплики, который создается. |

Чтобы создать реплику чтения между регионами, используйте параметр **Location** . В следующем примере создается реплика в регионе " **Западная часть США** ".

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Дополнительные сведения о том, в каких регионах можно создать реплику, см. в статье [об основных понятиях реплики чтения](concepts-read-replicas.md).

По умолчанию реплики чтения создаются с той же конфигурацией сервера, что и у главного, если не указан параметр **SKU** .

> [!NOTE]
> Чтобы сервер-реплика мог работать с главным сервером, рекомендуется, чтобы значения конфигурации сервера-реплики были равны или превосходили значения конфигурации главного сервера.

### <a name="list-replicas-for-a-master-server"></a>Перечисление реплик для главного сервера

Чтобы просмотреть все реплики для данного главного сервера, выполните следующую команду:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Для команды `Get-AzMariaDReplica` обязательны указанные ниже параметры.

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Группа ресурсов, в которой будет создан сервер реплики.  |
| ServerName | mydemoserver | Имя или идентификатор главного сервера. |

### <a name="delete-a-replica-server"></a>Удаление сервера-реплики

Для удаления сервера реплики чтения можно выполнить `Remove-AzPostgreSqlServer` командлет.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Удаление главного сервера

> [!IMPORTANT]
> Удаление главного сервера приводит к остановке репликации на все серверы-реплики и удалению самого главного сервера. Серверы-реплики становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

Чтобы удалить главный сервер, можно выполнить `Remove-AzPostgreSqlServer` командлет.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перезапуск базы данных Azure для сервера PostgreSQL с помощью PowerShell](howto-restart-server-powershell.md)
