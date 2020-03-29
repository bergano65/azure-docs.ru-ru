---
title: Управление репликами чтения - Azure CLI, REST API - База данных Azure для PostgreS'L - Единый сервер
description: Узнайте, как управлять считывательными репликами в базе данных Azure для PostgreS-L - Единый сервер от Azure CLI и REST API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774809"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Создавайте и управляйте считывательными репликами из Azure CLI, REST API

В этой статье вы узнаете, как создавать и управлять ремиссией чтения в базе данных Azure для PostgreS'L с помощью Azure CLI и REST API. Дополнительные сведения о репликах чтения см. в [этой статье](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Вы можете создавать и управлять считываемыми репликами с помощью ClI Azure.

### <a name="prerequisites"></a>Предварительные требования

- [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Сервер службы "База данных Azure для PostgreSQL"](quickstart-create-server-up-azure-cli.md) в качестве главного.


### <a name="prepare-the-master-server"></a>Подготовка главного сервера
Приведенные ниже действия позволяют подготовить главный сервер в ценовой категории "Общего назначения" или "Оптимизированная для операций в памяти".

На главном сервере задайте для параметра `azure.replication_support` значение **REPLICA**. При изменении этого статического параметра требуется перезагрузка сервера для вхастоя изменения.

1. Установить `azure.replication_support` на REPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Если вы получаете ошибку "Недействительное значение данного" при попытке установить azure.replication_support из Azure CLI, то, скорее всего, ваш сервер уже имеет REPLICA, установленный по умолчанию. Ошибка предотвращает правильное отражение этой настройки на новых серверах, где REPLICA является внутренним значением по умолчанию. <br><br>
> Вы можете пропустить подготовку мастер шаги и перейти к созданию реплики. <br><br>
> Если вы хотите подтвердить, что ваш сервер находится в этой категории, посетите страницу репликации сервера на портале Azure. "Неисполнимая репликация" будет седой, а "Добавить реплику" будет активна в панели инструментов.

2. Перезапустите сервер для применения изменения.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Создание реплики чтения

Команда [создания реплики сервера az postgres](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) требует следующих параметров:

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Группа ресурсов, где будет создан сервер реплики.  |
| name | mydemoserver-реплика | Имя нового сервера реплики, который создается. |
| source-server | mydemoserver | Имя или идентификатор ресурса существующего генерального сервера для репликации. |

В приведенном ниже примере CLI реплика создается в том же регионе, что и мастер.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Для создания реплики чтения поперечного региона используйте `--location` параметр. Пример CLI ниже создает реплику в Западной США.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Чтобы узнать больше о том, в каких регионах можно создать копию, посетите [статью о концепциях ремиссий.](concepts-read-replicas.md) 

Если вы не установили `azure.replication_support` параметр **REPLICA** на главном сервере общей цели или памяти, оптимизированный и перезапустили сервер, вы получите ошибку. Выполните эти два шага перед созданием реплики.

Реплика создается с помощью тех же параметров вычислений и хранения, что и мастер. После создания реплики вы можете независимо от главного сервера изменять следующие ее параметры: поколение вычислительных ресурсов, число виртуальных ядер, объем хранилища и период хранения резервных копий. Изменить также можно ценовую категорию (за исключением уровня "Базовый").

> [!IMPORTANT]
> Перед обновлением настройки сервера до нового значения обновите настройку реплики до равного или большего значения. Это действие помогает реплике идти в ногу с любыми изменениями, внесенными в мастер.

### <a name="list-replicas"></a>Список реплик
Вы можете просмотреть список реплик главного сервера с помощью команды [списка реплик серверов az postgres.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику
Вы можете остановить репликацию между мастер-сервером и ремиссией чтения с помощью команды [остановки стоп-копий сервера az postgres.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)

Остановленную репликацию отменить невозможно. Реплика чтения становится отдельным сервером, который поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Удаление сервера мастер или реплики
Чтобы удалить сервер master или replica, вы используете команду [удаления сервера az postgres.](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)

При удалении главного сервера репликация всех реплик чтения останавливается. Реплики чтения становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
Вы можете создавать и управлять считываемыми репликами с помощью [API Azure REST.](/rest/api/azure/)

### <a name="prepare-the-master-server"></a>Подготовка главного сервера
Приведенные ниже действия позволяют подготовить главный сервер в ценовой категории "Общего назначения" или "Оптимизированная для операций в памяти".

На главном сервере задайте для параметра `azure.replication_support` значение **REPLICA**. При изменении этого статического параметра требуется перезагрузка сервера для вхастоя изменения.

1. Установить `azure.replication_support` на REPLICA.

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

2. [Перезапустите сервер](/rest/api/postgresql/servers/restart) для применения изменения.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Создание реплики чтения
Вы можете создать реплику чтения с помощью [API создания:](/rest/api/postgresql/servers/create)

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
> Чтобы узнать больше о том, в каких регионах можно создать копию, посетите [статью о концепциях ремиссий.](concepts-read-replicas.md) 

Если вы не установили `azure.replication_support` параметр **REPLICA** на главном сервере общей цели или памяти, оптимизированный и перезапустили сервер, вы получите ошибку. Выполните эти два шага перед созданием реплики.

Реплика создается с помощью тех же параметров вычислений и хранения, что и мастер. После создания реплики вы можете независимо от главного сервера изменять следующие ее параметры: поколение вычислительных ресурсов, число виртуальных ядер, объем хранилища и период хранения резервных копий. Изменить также можно ценовую категорию (за исключением уровня "Базовый").


> [!IMPORTANT]
> Перед обновлением настройки сервера до нового значения обновите настройку реплики до равного или большего значения. Это действие помогает реплике идти в ногу с любыми изменениями, внесенными в мастер.

### <a name="list-replicas"></a>Список реплик
Вы можете просмотреть список реплик главного сервера с помощью [API списка реплик:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику
Можно остановить репликацию между мастер-сервером и репликой чтения с помощью [API обновления.](/rest/api/postgresql/servers/update)

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

### <a name="delete-a-master-or-replica-server"></a>Удаление сервера мастер или реплики
Чтобы удалить сервер master или replica, вы используете [API удаления:](/rest/api/postgresql/servers/delete)

При удалении главного сервера репликация всех реплик чтения останавливается. Реплики чтения становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Дальнейшие действия
* См. дополнительные сведения о [репликах чтения в Базе данных Azure для PostgreSQL](concepts-read-replicas.md).
* Дополнительные сведения см. в статье [Создание реплик чтения и управление ими с помощью портала Azure](howto-read-replicas-portal.md).
