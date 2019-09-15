---
title: Управление репликами чтения для базы данных Azure для PostgreSQL — один сервер из Azure CLI REST API
description: Узнайте, как управлять репликами чтения в базе данных Azure для PostgreSQL-Single Server из Azure CLI и REST API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e146a0f42b6487545321ebfbc9ec523da5e78c8a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995356"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Создание реплик чтения и управление ими из Azure CLI REST API

Из этой статьи вы узнаете, как создавать реплики чтения и управлять ими в базе данных Azure для PostgreSQL с помощью Azure CLI и REST API. Дополнительные сведения о репликах чтения см. в [этой статье](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Вы можете создавать реплики чтения и управлять ими с помощью Azure CLI.

### <a name="prerequisites"></a>Предварительные требования

- [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Сервер службы "База данных Azure для PostgreSQL"](quickstart-create-server-up-azure-cli.md) в качестве главного.


### <a name="prepare-the-master-server"></a>Подготовка главного сервера
Приведенные ниже действия позволяют подготовить главный сервер в ценовой категории "Общего назначения" или "Оптимизированная для операций в памяти".

На главном сервере задайте для параметра `azure.replication_support` значение **REPLICA**. При изменении этого статического параметра необходимо перезапустить сервер, чтобы изменения вступили в силу.

1. Задайте `azure.replication_support` для параметра значение реплика.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Перезапустите сервер, чтобы применить изменение.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Создание реплики чтения

Команда [AZ postgres Server Replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) требует наличия следующих параметров:

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Группа ресурсов, в которой будет создан сервер реплики.  |
| name | mydemoserver — реплика | Имя нового сервера реплики, который создается. |
| source-server | mydemoserver | Имя или идентификатор ресурса существующего главного сервера, с которого выполняется репликация. |

В следующем примере CLI реплика создается в том же регионе, что и Главная.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Чтобы создать реплику чтения между регионами, используйте `--location` параметр. Приведенный ниже пример интерфейса командной строки создает реплику в западной части США.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Дополнительные сведения о том, в каких регионах можно создать реплику, см. в [статье чтение основных сведений о репликах](concepts-read-replicas.md). 

Если `azure.replication_support` параметр не задан для **реплики** на общего назначения или на главном сервере, оптимизированном для памяти, и сервер перезагружен, появится сообщение об ошибке. Перед созданием реплики выполните эти два действия.

Реплика создается с использованием тех же параметров вычислений и хранилища, что и у главного сервера. После создания реплики вы можете независимо от главного сервера изменять следующие ее параметры: поколение вычислительных ресурсов, число виртуальных ядер, объем хранилища и период хранения резервных копий. Изменить также можно ценовую категорию (за исключением уровня "Базовый").

> [!IMPORTANT]
> Перед обновлением параметра главного сервера до нового значения измените значение параметра реплики на равное или большее. Это действие помогает реплике справиться с изменениями, внесенными в главную базу.

### <a name="list-replicas"></a>Список реплик
Список реплик главного сервера можно просмотреть с помощью команды [AZ postgres Server Replica List](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику
Репликацию между главным сервером и репликой чтения можно отключить с помощью команды [AZ postgres Server Replica](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Остановленную репликацию отменить невозможно. Реплика чтения становится отдельным сервером, который поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Удаление главного или сервер-реплики
Чтобы удалить главный или сервер-реплику, используйте команду [AZ postgres Server DELETE](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

При удалении главного сервера репликация всех реплик чтения останавливается. Реплики чтения становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
Вы можете создавать реплики чтения и управлять ими с помощью [REST API Azure](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Подготовка главного сервера
Приведенные ниже действия позволяют подготовить главный сервер в ценовой категории "Общего назначения" или "Оптимизированная для операций в памяти".

На главном сервере задайте для параметра `azure.replication_support` значение **REPLICA**. При изменении этого статического параметра необходимо перезапустить сервер, чтобы изменения вступили в силу.

1. Задайте `azure.replication_support` для параметра значение реплика.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Перезапустите сервер](/rest/api/postgresql/servers/restart) , чтобы применить изменение.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Создание реплики чтения
Вы можете создать реплику чтения с помощью [API создания](/rest/api/postgresql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Дополнительные сведения о том, в каких регионах можно создать реплику, см. в [статье чтение основных сведений о репликах](concepts-read-replicas.md). 

Если `azure.replication_support` параметр не задан для **реплики** на общего назначения или на главном сервере, оптимизированном для памяти, и сервер перезагружен, появится сообщение об ошибке. Перед созданием реплики выполните эти два действия.

Реплика создается с использованием тех же параметров вычислений и хранилища, что и у главного сервера. После создания реплики вы можете независимо от главного сервера изменять следующие ее параметры: поколение вычислительных ресурсов, число виртуальных ядер, объем хранилища и период хранения резервных копий. Изменить также можно ценовую категорию (за исключением уровня "Базовый").


> [!IMPORTANT]
> Перед обновлением параметра главного сервера до нового значения измените значение параметра реплики на равное или большее. Это действие помогает реплике справиться с изменениями, внесенными в главную базу.

### <a name="list-replicas"></a>Список реплик
Список реплик главного сервера можно просмотреть с помощью [API списка реплик](/rest/api/postgresql/replicas/listbyserver).

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику
Репликацию между главным сервером и репликой чтения можно отключить с помощью [API обновления](/rest/api/postgresql/servers/update).

Остановленную репликацию отменить невозможно. Реплика чтения становится отдельным сервером, который поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Удаление главного или сервер-реплики
Для удаления главного или сервер-реплики используется [API удаления](/rest/api/postgresql/servers/delete).

При удалении главного сервера репликация всех реплик чтения останавливается. Реплики чтения становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Следующие шаги
* См. дополнительные сведения о [репликах чтения в Базе данных Azure для PostgreSQL](concepts-read-replicas.md).
* Дополнительные сведения см. в статье [Создание реплик чтения и управление ими с помощью портала Azure](howto-read-replicas-portal.md).