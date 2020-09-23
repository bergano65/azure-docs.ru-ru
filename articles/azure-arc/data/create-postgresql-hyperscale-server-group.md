---
title: Создание группы серверов масштабирования базы данных Azure для PostgreSQL в службе "Дуга Azure"
description: Создание группы серверов масштабирования базы данных Azure для PostgreSQL в службе "Дуга Azure"
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e845136c4fed5a3d2e6863fdab0aa9f70fb30b5d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939922"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Создание группы серверов PostgreSQL в службе "Дуга Azure" с поддержкой геомасштабирования

В этом документе описаны шаги по созданию группы PostgreSQL Scale Server в службе "Дуга" Azure.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Начало работы
Если вы уже знакомы с приведенными ниже разделами, этот абзац можно пропустить.
Прежде чем продолжить создание, необходимо ознакомиться с важными разделами.
- [Обзор служб данных с поддержкой дуги Azure](overview.md)
- [Режимы подключения и требования](connectivity.md)
- [Основные понятия конфигурации хранилища и хранилища Kubernetes](storage-configuration.md)
- [Модель ресурсов Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Если вы предпочитаете работать без подготовки полной среды самостоятельно, начните работу с помощью [Azure Arc JumpStart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) в службе Azure Kubernetes Service (AKS), AWS эластичной Kubernetes Service (ЕКС), Google Cloud Kubernetes Engine (гке) или на виртуальной машине Azure.


## <a name="login-to-the-azure-arc-data-controller"></a>Вход в контроллер данных ARC в Azure

Перед созданием экземпляра необходимо сначала войти в систему на контроллере данных ARC в Azure. Если вы уже вошли в контроллер данных, этот шаг можно пропустить.

```console
azdata login
```

Затем будет предложено ввести имя пользователя, пароль и пространство имен System.  

> Если вы использовали скрипт для создания контроллера данных, пространство имен должно быть **дугой** .

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Предварительный и временный шаг только для пользователей OpenShift

Реализуйте этот шаг перед переходом к следующему шагу. Чтобы развернуть группу серверов PostgreSQL в Red Hat OpenShift в проекте, отличном от используемого по умолчанию, необходимо выполнить следующие команды в кластере, чтобы обновить ограничения безопасности. Эта команда предоставляет необходимые привилегии учетным записям служб, которые будут выполнять группу серверов PostgreSQL. Ограничение контекста безопасности (SCC) **_Arc-Data-SCC_** добавляется при развертывании контроллера данных ARC в Azure.

```console
oc adm policy add-scc-to-group arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-Name** — это имя группы серверов, которая будет создана на следующем шаге._
   
Дополнительные сведения о SCC в OpenShift см. в [документации по OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Теперь вы можете реализовать следующий шаг.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Создание группы серверов масштабирования базы данных Azure для PostgreSQL

Чтобы создать группу серверов "база данных Azure для PostgreSQL" в службе "Дуга" Azure, используйте следующую команду:

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **Доступны другие параметры командной строки.  Просмотрите полный список параметров, выполнив `azdata arc postgres server create --help` .**
> - В предварительной версии для резервного копирования и восстановления необходимо указать класс хранения для резервных копий (_--Storage-класса-Backups-СКБ_) во время создания группы серверов.
> - Единица, принимаемая параметрами--Volume-Size-*, — это Kubernetes ресурсное количество (целое число, за которым следует одно из этих данных, достаточное (T, G, M, K, M) или их степень-два эквивалента (Ti, МВт, MI, KI)).
> - Длина имен не должна превышать 10 символов и соответствовать соглашениям об именах DNS.
> - Вам будет предложено ввести пароль для пользователя с правами администратора _postgres_ Standard.  Интерактивную строку можно пропустить, задав `AZDATA_PASSWORD` переменную среды сеанса перед выполнением команды Create.
> - Если контроллер данных развернут с помощью AZDATA_USERNAME и AZDATA_PASSWORD в том же сеансе терминала, то значения AZDATA_USERNAME и AZDATA_PASSWORD будут использоваться для развертывания группы серверов PostgreSQL в масштабе. Имя пользователя-администратора по умолчанию для ядра СУБД PostgreSQL _PostgreSQL_ не может быть изменено на этом этапе.
> - Создание группы серверов PostgreSQL Scale не приводит к немедленной регистрации ресурсов в Azure. В рамках процесса отправки данных [инвентаризации](upload-metrics-and-logs-to-azure-monitor.md)  или [использования](view-billing-data-in-azure.md) ресурсов в Azure ресурсы будут созданы в Azure, а ресурсы будут доступны в портал Azure.
> - На этом этапе невозможно изменить параметр--Port.
> - Если в кластере Kubernetes нет класса хранения по умолчанию, необходимо использовать параметр--Метадатасторажекласс, чтобы указать его. Это приведет к сбою команды Create. Чтобы проверить наличие класса хранилища по умолчанию, объявленного в кластере Kubernetes, обзваниваются следующую команду: 
>
>   ```console
>   kubectl get sc
>   ```
>
> - Если существует класс хранения, настроенный как класс хранения по умолчанию, будет добавлено имя класса хранения **(по умолчанию)** . Пример:
>
>   ```output
>   NAME                       PROVISIONER                        AGE
>   local-storage (default)    kubernetes.io/no-provisioner       4d18h
>   ```


## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Вывод списка групп серверов базы данных Azure для PostgreSQL, созданных в вашей настройке Arc

Чтобы просмотреть группы PostgreSQL Scale Server в службе "Дуга Azure", используйте следующую команду:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Получение конечных точек для подключения к группам серверов базы данных Azure для PostgreSQL

Чтобы просмотреть конечные точки для экземпляра PostgreSQL, выполните следующую команду:

```console
azdata arc postgres endpoint list -n <server group name>
```
Пример:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Конечную точку экземпляра PostgreSQL можно использовать для подключения к группе PostgreSQL Scale Server из избранного средства:  [Azure Data Studio](https://aka.ms/getazuredatastudio), [Пгкли](https://www.pgcli.com/) PSQL, pgAdmin и т. д.

Если вы используете виртуальную машину Azure для тестирования, следуйте приведенным ниже инструкциям.

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Специальное примечание о развертывании виртуальных машин Azure

При использовании виртуальной машины Azure IP-адрес конечной точки не будет показывать _общедоступный_ IP-адрес. Чтобы узнать общедоступный IP-адрес, используйте следующую команду:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Затем можно объединить общедоступный IP-адрес с портом, чтобы установить соединение.

Также может потребоваться предоставить порт группы PostgreSQL Scale Server через шлюз сетевой безопасности (NSG). Чтобы разрешить трафик через (NSG), необходимо добавить правило, которое можно выполнить с помощью следующей команды:

Чтобы задать правило, необходимо знать имя NSG. Определить NSG можно с помощью следующей команды:

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

После получения имени NSG можно добавить правило брандмауэра, используя следующую команду. Приведенные здесь примеры значений создают правило NSG для порта 30655 и допускают подключение с **любого** исходного IP-адреса.  Это не рекомендуется по соображениям безопасности.  Вы можете лучше зафиксировать вещи, указав значение-Source-Address-префиксы, характерные для IP-адреса клиента или диапазон IP-адресов, который охватывает IP-адреса вашей команды или организации.

Замените значение параметра--destination-Port-Ranges на номер порта, полученный в приведенной выше команде "аздата Arc postgres Server List".

```console
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Подключение к Azure Data Studio

Откройте Azure Data Studio и подключитесь к экземпляру с помощью IP-адреса внешней конечной точки и номера порта выше, а также пароля, указанного во время создания экземпляра.  Если PostgreSQL недоступен в раскрывающемся списке *тип подключения* , можно установить расширение PostgreSQL, выполнив поиск по имени PostgreSQL на вкладке расширения.

> [!NOTE]
> Необходимо нажать кнопку [дополнительно] на панели подключения, чтобы ввести номер порта.

Помните, что при использовании виртуальной машины Azure потребуется _общедоступный_ IP-адрес, доступный с помощью следующей команды:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Подключение с помощью psql

Чтобы получить доступ к группе серверов PostgreSQL Scale, передайте внешнюю конечную точку группы серверов PostgreSQL, полученной выше:

Теперь можно подключить любой из psql:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с основными понятиями и руководствами по использованию службы "база данных Azure для PostgreSQL", чтобы распределить данные между несколькими узлами PostgreSQL, а также получить преимущества от всех возможностей службы "база данных Azure для PostgreSQL". :
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
