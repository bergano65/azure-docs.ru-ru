---
title: Управление репликами чтения - Azure CLI, REST API - База данных Azure для MariaDB
description: В этой статье описывается, как настроить и управлять репликами чтения в базе данных Azure для MariaDB с помощью Azure CLI и REST API.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: abf80e98881b73bed53c5a939a79bc8b3a9de2db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530586"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Как создавать и управлять считывательными репликами в базе данных Azure для MariaDB с помощью Azure CLI и REST API

В этой статье вы узнаете, как создавать и управлять считывательными репликами в базе данных Azure для службы MariaDB с помощью Azure CLI и REST API.

## <a name="azure-cli"></a>Azure CLI
Вы можете создавать и управлять считываемыми репликами с помощью ClI Azure.

### <a name="prerequisites"></a>Предварительные требования

- [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- [База данных Azure для сервера MariaDB,](quickstart-create-mariadb-server-database-using-azure-portal.md) которая будет использоваться в качестве главного сервера. 

> [!IMPORTANT]
> Функция считывателя реплики доступна только для базы данных Azure для серверов MariaDB на уровнях общего назначения или оптимизации цен на память. Убедитесь, что главный сервер находится в одной из этих ценовых категорий.

### <a name="create-a-read-replica"></a>Создание реплики чтения

Чтобы создать сервер реплики чтения, выполните следующую команду:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Для команды `az mariadb server replica create` обязательны указанные ниже параметры.

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой будет создан сервер реплики.  |
| name | mydemoreplicaserver | Имя нового сервера реплики, который создается. |
| source-server | mydemoserver | Имя или идентификатор имеющегося главного сервера для репликации. |

Для создания реплики чтения поперечного региона используйте `--location` параметр. 

> [!NOTE]
> Репликация кросс-региона находится в предварительном просмотре.

Пример CLI ниже создает реплику в Западной США.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Чтобы узнать больше о том, в каких регионах можно создать копию, посетите [статью о концепциях ремиссий.](concepts-read-replicas.md) 

> [!NOTE]
> Реплики чтения создаются с той же конфигурацией сервера, что и у главного сервера. Вы можете изменить созданную конфигурацию сервера-реплики. Чтобы сервер-реплика мог работать с главным сервером, рекомендуется, чтобы значения конфигурации сервера-реплики были равны или превосходили значения конфигурации главного сервера.

### <a name="list-replicas-for-a-master-server"></a>Перечисление реплик для главного сервера

Чтобы просмотреть все реплики для данного главного сервера, выполните следующую команду: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Для команды `az mariadb server replica list` обязательны указанные ниже параметры.

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой будет создан сервер реплики.  |
| server-name | mydemoserver | Имя или идентификатор главного сервера. |

### <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику

> [!IMPORTANT]
> Остановка репликации на сервер является необратимой операцией. Репликацию, остановленную между главным сервером и сервером-репликой, отменить невозможно. Сервер-реплика становится автономным и начинает поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

Репликацию на сервер реплики чтения можно остановить, выполнив следующую команду:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Для команды `az mariadb server replica stop` обязательны указанные ниже параметры.

| Параметр | Пример значения | Описание  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой находится сервер реплики.  |
| name | mydemoreplicaserver | Имя сервера реплики для остановки репликации. |

### <a name="delete-a-replica-server"></a>Удаление сервера-реплики

Удаление сервера считываемых реплик может быть сделано путем запуска команды **[удаления сервера az mariadb.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Удаление главного сервера

> [!IMPORTANT]
> Удаление главного сервера приводит к остановке репликации на все серверы-реплики и удалению самого главного сервера. Серверы-реплики становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

Чтобы удалить основной сервер, можно запустить команду **[удаления сервера az mariadb.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
Вы можете создавать и управлять считываемыми репликами с помощью [API Azure REST.](/rest/api/azure/)

### <a name="create-a-read-replica"></a>Создание реплики чтения
Вы можете создать реплику чтения с помощью [API создания:](/rest/api/mariadb/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
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
Вы можете просмотреть список реплик главного сервера с помощью [API списка реплик:](/rest/api/mariadb/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику
Можно остановить репликацию между мастер-сервером и репликой чтения с помощью [API обновления.](/rest/api/mariadb/servers/update)

Остановленную репликацию отменить невозможно. Реплика чтения становится отдельным сервером, который поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Удаление сервера мастер или реплики
Чтобы удалить сервер master или replica, вы используете [API удаления:](/rest/api/mariadb/servers/delete)

При удалении главного сервера репликация всех реплик чтения останавливается. Реплики чтения становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [репликах чтения](concepts-read-replicas.md)
