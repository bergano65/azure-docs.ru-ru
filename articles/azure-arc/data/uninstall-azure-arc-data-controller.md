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
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939109"
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

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>При необходимости удалите пространство имен контроллера данных Arc Azure.


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Дальнейшие действия

[Что такое службы данных с поддержкой дуги Azure?](overview.md)