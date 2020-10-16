---
title: Получение журналов для устранения неполадок контроллера данных с поддержкой ARC в Azure
description: Получение журналов службы для устранения неполадок контроллера данных с поддержкой ARC в Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939118"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Получение журналов служб данных с поддержкой дуги Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Предварительные требования

Для получения журналов служб данных, включенных в дугу Azure, вам потребуется средство Azure Data CLI. [Инструкции по установке](./install-client-tools.md)

Необходимо иметь возможность войти в службу контроллера служб данных с включенной службой "Дуга Azure" с правами администратора.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Получение журналов служб данных с поддержкой дуги Azure

Вы можете получить журналы служб данных для службы "Дуга Azure" во всех модулях Pod или конкретных модулях для устранения неполадок.  Это можно сделать с помощью стандартных средств Kubernetes, таких как `kubectl logs` команда или в этой статье, вы будете использовать средство CLI для данных Azure, которое упрощает получение всех журналов одновременно.

Сначала убедитесь, что вы вошли в контроллер данных.

```console
azdata login
```

Затем выполните следующую команду, чтобы создать дамп журналов:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

Файлы журнала будут созданы в текущем рабочем каталоге по умолчанию в подкаталоге с именем Logs.  Вы можете вывести файлы журнала в другой каталог с помощью `--target-folder` параметра.

Можно выбрать сжатие файлов, опустив `--skip-compress` параметр.

Можно активировать и включить дампы памяти, опустив `--exclude-dumps` , но это не рекомендуется, если только служба поддержки Майкрософт не запросил дампы памяти.  Для создания дампа памяти необходимо, чтобы параметр контроллера данных был установлен в значение, заданное `allowDumps` `true` при создании контроллера данных.

При необходимости можно выбрать фильтр для регистрации журналов только для определенного Pod ( `--pod` ) или контейнера ( `--container` ) по имени.

Можно также выбрать фильтр для получения журналов для определенного настраиваемого ресурса, передав параметр `--resource-kind` и `--resource-name` .  `resource-kind`Значение параметра должно быть одним из имен настраиваемых определений ресурсов, которые могут быть получены командой `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Пример иерархии папок.  Обратите внимание, что иерархия папок упорядочена по имени POD, а затем по контейнеру, а затем по иерархии каталогов в контейнере.

```console
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```