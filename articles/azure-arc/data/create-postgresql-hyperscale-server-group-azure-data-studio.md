---
title: Создание PostgreSQL с поддержкой дуги Azure с помощью Azure Data Studio
description: Создание PostgreSQL с поддержкой дуги Azure с помощью Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: af0cdb814433b739b15d79bec9cb399cf0a2fef7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939923"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Создание PostgreSQL с поддержкой дуги Azure с помощью Azure Data Studio

В этом документе описано, как с помощью Azure Data Studio подготавливать группы серверов PostgreSQL с поддержкой дуги Azure.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Подключение к контроллеру данных ARC в Azure

Перед созданием экземпляра Войдите в контроллер данных ARC в Azure, если вы еще не вошли в систему.

```console
azdata login
```

Затем будет предложено ввести пространство имен, в котором создается контроллер данных, имя пользователя и пароль для входа на контроллер.

> Если необходимо проверить пространство имен, можно запустить, ```kubectl get pods -A``` чтобы получить список всех пространств имен в кластере.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Создание группы серверов PostgreSQL в службе "Дуга Azure" с поддержкой геомасштабирования

1. Запустить Azure Data Studio
1. На вкладке подключения щелкните три точки в верхнем левом углу и выберите "создать развертывание".
1. В параметрах развертывания выберите **PostgreSQL Scale Server Group (Azure ARC)** .
    >[!NOTE]
    > Возможно, вам будет предложено установить `azdata` CLI, если он в настоящее время не установлен.
1. Примите условия соглашения о конфиденциальности и лицензии и нажмите кнопку **выбрать** внизу.
1. В колонке развернуть PostgreSQL-масштабируемый сервер в группе Azure Arc введите следующие сведения:
   - Введите имя группы серверов
   - Введите и подтвердите пароль для пользователя _postgres_ с правами администратора группы серверов.
   - Выберите класс хранения данных, соответствующий
   - Выберите класс хранения в качестве подходящего для журналов.
   - Выберите класс хранения, соответствующий резервным копиям
   - Выберите число рабочих узлов для инициализации
1. Нажмите кнопку " **развернуть** "

Будет запущено создание группы серверов PostgreSQL в службе "Дуга Azure" с контроллером данных.

Через несколько минут создание будет успешно завершено.

## <a name="next-steps"></a>Дальнейшие действия
- [Управление группой серверов с помощью Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Мониторинг группы серверов](monitor-grafana-kibana.md)
- Ознакомьтесь с основными понятиями и руководствами по использованию службы "база данных Azure для PostgreSQL", чтобы распределить данные между несколькими узлами PostgreSQL, а также получить преимущества от всех возможностей службы "база данных Azure для postgres". :
    * [Узлы и таблицы](../../postgresql/concepts-hyperscale-nodes.md)
    * [Определение типа приложения](../../postgresql/concepts-hyperscale-app-type.md)
    * [Выбор столбца распределения](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Совместное размещение таблиц](../../postgresql/concepts-hyperscale-colocation.md)
    * [Распространение и изменение таблиц](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Проектирование базы данных с несколькими клиентами](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Разработка панели мониторинга для аналитики в режиме реального времени](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* В приведенных выше документах пропустите разделы **Вход в портал Azure**& **создать базу данных Azure для PostgreSQL-Scale (Цитус)**. Реализуйте оставшиеся шаги в развертывании Azure ARC. Эти разделы относятся к службе "база данных Azure для PostgreSQL" (Цитус), предлагаемой в качестве службы PaaS в облаке Azure, но другие части документов напрямую применимы к вашей геомасштабированию Azure с включенной PostgreSQL.

- [Масштабирование группы серверов PostgreSQL в базе данных Azure для масштабирования](scale-out-postgresql-hyperscale-server-group.md)
- [Основные понятия конфигурации хранилища и хранилища Kubernetes](storage-configuration.md)
- [Развертывание утверждений Постоянного тома](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Модель ресурсов Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

