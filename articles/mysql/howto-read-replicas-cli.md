---
title: Создание реплик чтения и управление ими в базе данных Azure для MySQL
description: В этой статье описано, как настраивать реплики чтения и управлять ими в Базе данных Azure для MySQL с помощью Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 052df8d5eb99f13b9ab0bbf41b32a9f0386f8cc0
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539574"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Создание реплик чтения и управление ими в Базе данных Azure для MySQL с помощью Azure CLI

В этой статье вы узнаете, как создать реплики чтения и управлять ими в одном регионе Azure в службе "База данных Azure для MySQL" с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md), который будет использоваться в качестве главного сервера. 

> [!IMPORTANT]
> Функция создания реплики чтения доступна только для серверов базы данных Azure для MySQL в ценовой категории "Общее назначение" или "Оптимизированная для операций в памяти". Убедитесь, что главный сервер находится в одной из этих ценовых категорий.

## <a name="create-a-read-replica"></a>Создание реплики чтения

Чтобы создать сервер реплики чтения, выполните следующую команду:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Для команды `az mysql server replica create` обязательны указанные ниже параметры.

| Параметр | Пример значения | ОПИСАНИЕ  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой будет создан сервер реплики.  |
| name | mydemoreplicaserver | Имя нового сервера реплики, который создается. |
| source-server | mydemoserver | Имя или идентификатор имеющегося главного сервера для репликации. |

> [!NOTE]
> Реплики чтения создаются с той же конфигурацией сервера, что и у главного сервера. Вы можете изменить созданную конфигурацию сервера-реплики. Чтобы сервер-реплика мог работать с главным сервером, рекомендуется, чтобы значения конфигурации сервера-реплики были равны или превосходили значения конфигурации главного сервера.

## <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику

> [!IMPORTANT]
> Остановка репликации на сервер является необратимой операцией. Репликацию, остановленную между главным сервером и сервером-репликой, отменить невозможно. Сервер-реплика становится автономным и начинает поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

Репликацию на сервер реплики чтения можно остановить, выполнив следующую команду:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Для команды `az mysql server replica stop` обязательны указанные ниже параметры.

| Параметр | Пример значения | ОПИСАНИЕ  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой находится сервер реплики.  |
| name | mydemoreplicaserver | Имя сервера реплики для остановки репликации. |

## <a name="delete-a-replica-server"></a>Удаление сервера-реплики

Сервер реплики чтения можно удалить, выполнив команду **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)**.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Удаление главного сервера

> [!IMPORTANT]
> Удаление главного сервера приводит к остановке репликации на все серверы-реплики и удалению самого главного сервера. Серверы-реплики становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

Чтобы удалить главный сервер, можно выполнить команду **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)**.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Перечисление реплик для главного сервера

Чтобы просмотреть все реплики для данного главного сервера, выполните следующую команду: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Для команды `az mysql server replica list` обязательны указанные ниже параметры.

| Параметр | Пример значения | ОПИСАНИЕ  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой будет создан сервер реплики.  |
| server-name | mydemoserver | Имя или идентификатор главного сервера. |

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о [репликах чтения](concepts-read-replicas.md)
