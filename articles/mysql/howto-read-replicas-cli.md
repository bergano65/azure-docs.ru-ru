---
title: Управление репликами чтения — Azure CLI, REST API — база данных Azure для MySQL
description: Узнайте, как настроить реплики чтения и управлять ими в базе данных Azure для MySQL с помощью Azure CLI или REST API.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 56ba530c4f684bf89db9c5b87306592fbfeee7fa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774100"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Как создавать реплики чтения и управлять ими в базе данных Azure для MySQL с помощью Azure CLI и REST API

В этой статье вы узнаете, как создавать реплики чтения и управлять ими в службе "база данных Azure для MySQL" с помощью Azure CLI и REST API. Дополнительные сведения о репликах чтения см. в [этой статье](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Вы можете создавать реплики чтения и управлять ими с помощью Azure CLI.

### <a name="prerequisites"></a>Технические условия

- [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md), который будет использоваться в качестве главного сервера. 

> [!IMPORTANT]
> Функция создания реплики чтения доступна только для серверов базы данных Azure для MySQL в ценовой категории "Общее назначение" или "Оптимизированная для операций в памяти". Убедитесь, что главный сервер находится в одной из этих ценовых категорий.

### <a name="create-a-read-replica"></a>Создание реплики чтения

Чтобы создать сервер реплики чтения, выполните следующую команду:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Для команды `az mysql server replica create` обязательны указанные ниже параметры.

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой будет создан сервер реплики.  |
| name | mydemoreplicaserver | Имя нового сервера реплики, который создается. |
| source-server | mydemoserver | Имя или идентификатор имеющегося главного сервера для репликации. |

Чтобы создать реплику чтения между регионами, используйте параметр `--location`. Приведенный ниже пример интерфейса командной строки создает реплику в западной части США.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Дополнительные сведения о том, в каких регионах можно создать реплику, см. в [статье чтение основных сведений о репликах](concepts-read-replicas.md). 

> [!NOTE]
> Реплики чтения создаются с той же конфигурацией сервера, что и у главного сервера. Вы можете изменить созданную конфигурацию сервера-реплики. Чтобы сервер-реплика мог работать с главным сервером, рекомендуется, чтобы значения конфигурации сервера-реплики были равны или превосходили значения конфигурации главного сервера.


### <a name="list-replicas-for-a-master-server"></a>Перечисление реплик для главного сервера

Чтобы просмотреть все реплики для данного главного сервера, выполните следующую команду: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Для команды `az mysql server replica list` обязательны указанные ниже параметры.

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой будет создан сервер реплики.  |
| server-name | mydemoserver | Имя или идентификатор главного сервера. |

### <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику

> [!IMPORTANT]
> Остановка репликации на сервер является необратимой операцией. Репликацию, остановленную между главным сервером и сервером-репликой, отменить невозможно. Сервер-реплика становится автономным и начинает поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

Репликацию на сервер реплики чтения можно остановить, выполнив следующую команду:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Для команды `az mysql server replica stop` обязательны указанные ниже параметры.

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой находится сервер реплики.  |
| name | mydemoreplicaserver | Имя сервера реплики для остановки репликации. |

### <a name="delete-a-replica-server"></a>Удаление сервера-реплики

Сервер реплики чтения можно удалить, выполнив команду **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Удаление главного сервера

> [!IMPORTANT]
> Удаление главного сервера приводит к остановке репликации на все серверы-реплики и удалению самого главного сервера. Серверы-реплики становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

Чтобы удалить главный сервер, можно выполнить команду **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
Вы можете создавать реплики чтения и управлять ими с помощью [REST API Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Создание реплики чтения
Вы можете создать реплику чтения с помощью [API создания](/rest/api/mysql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Дополнительные сведения о том, в каких регионах можно создать реплику, см. в [статье чтение основных сведений о репликах](concepts-read-replicas.md). 

Если вы не установили параметр `azure.replication_support` для **реплики** на общего назначения или на главном сервере, оптимизированном для памяти, и перезапустили сервер, появится сообщение об ошибке. Перед созданием реплики выполните эти два действия.

Реплика создается с использованием тех же параметров вычислений и хранилища, что и у главного сервера. После создания реплики вы можете независимо от главного сервера изменять следующие ее параметры: поколение вычислительных ресурсов, число виртуальных ядер, объем хранилища и период хранения резервных копий. Изменить также можно ценовую категорию (за исключением уровня "Базовый").


> [!IMPORTANT]
> Перед обновлением параметра главного сервера до нового значения измените значение параметра реплики на равное или большее. Это действие помогает реплике справиться с изменениями, внесенными в главную базу.

### <a name="list-replicas"></a>Список реплик
Список реплик главного сервера можно просмотреть с помощью [API списка реплик](/rest/api/mysql/replicas/listbyserver).

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику
Репликацию между главным сервером и репликой чтения можно отключить с помощью [API обновления](/rest/api/mysql/servers/update).

Остановленную репликацию отменить невозможно. Реплика чтения становится отдельным сервером, который поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Удаление главного или сервер-реплики
Для удаления главного или сервер-реплики используется [API удаления](/rest/api/mysql/servers/delete).

При удалении главного сервера репликация всех реплик чтения останавливается. Реплики чтения становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [репликах чтения](concepts-read-replicas.md)
