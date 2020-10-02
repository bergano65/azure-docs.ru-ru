---
title: Удаление контроллера данных ARC в Azure
description: Удаление контроллера данных ARC в Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7e1a2d50e04601b977bb7a708f60e78089ddded1
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631024"
---
# <a name="delete-azure-arc-data-controller"></a>Удаление контроллера данных ARC в Azure

В следующей статье описывается удаление контроллера данных ARC в Azure.

Прежде чем продолжать, убедитесь, что все службы данных, созданные на контроллере данных, удалены следующим образом.

## <a name="log-in-to-the-data-controller"></a>Вход в контроллер данных

Войдите в контроллер данных, который необходимо удалить:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Перечисление & удаление существующих служб данных

Выполните следующую команду, чтобы проверить, созданы ли управляемые экземпляры SQL:

```
azdata arc sql mi list
```

Для каждого управляемого экземпляра SQL из приведенного выше списка выполните команду Delete следующим образом:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Аналогично, чтобы проверить экземпляры PostgreSQL Scale, выполните:

```
azdata arc postgres server list
```

Для каждого экземпляра PostgreSQL Scale выполните команду Delete следующим образом:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Удаление контроллера

После удаления всех экземпляров SQL, управляемых экземплярами и PostgreSQL, контроллер данных можно удалить следующим образом:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Удалить SCC (только Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Удаление объектов уровня кластера

Кроме объектов пространства имен, если также требуется удалить объекты уровня кластера, такие как КРДС, `clusterroles` и `clusterrolebindings` , выполните следующие команды:

```
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>При необходимости удалите пространство имен контроллера данных Arc Azure.


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Дальнейшие действия

[Что такое службы данных с поддержкой дуги Azure?](overview.md)
