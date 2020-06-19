---
title: Создание подключения субъекта-службы с поддержкой Azure Arc (предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Создание подключения субъекта-службы с поддержкой Azure Arc '
keywords: Kubernetes, Arc, Azure, контейнеры
ms.openlocfilehash: 3c95c6bb85c7c1bc097b7751a560a658863c0afd
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725607"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Создание подключения субъекта-службы с поддержкой Azure Arc (предварительная версия)

## <a name="overview"></a>Обзор

Субъект-службы могут использовать назначение ролей с ограниченными привилегиями для подключения кластеров Kubernetes к службе Azure Arc. Это полезно в конвейерах непрерывной интеграции и непрерывного развертывания (CI/CD), таких как Azure Pipelines и GitHub Actions.

Ниже приведены пошаговые инструкции по использованию субъект-служб для подключения кластеров Kubernetes к службе Azure Arc.

## <a name="create-a-new-service-principal"></a>Создание субъект-службы

Создайте субъект-службу, предоставив для нее информативное имя. Обратите внимание, что это имя должно быть уникальным в клиенте Azure Active Directory:

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Выходные данные:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Назначение разрешений

После создания субъект-службы назначьте роль "Подключение Azure Arc для Kubernetes" только что созданному участнику. Это встроенная роль Azure с ограниченными разрешениями, которая позволяет субъекту только регистрировать кластеры в Azure. Субъект не может обновлять, удалять или изменять другие кластеры или ресурсы в подписке.

Учитывая ограниченные возможности, клиенты могут легко повторно использовать этот субъект для подключения нескольких кластеров.

Разрешения можно ограничить еще больше, передав соответствующий аргумент `--scope` при назначении роли. Это позволяет клиентам ограничивать регистрацию в кластере. Следующие сценарии поддерживаются различными параметрами `--scope`:

| Ресурс  | Аргумент `scope`| Действие |
| ------------- | ------------- | ------------- |
| Подписка | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Субъект-служба может зарегистрировать любой кластер в существующей группе ресурсов в заданной подписке. |
| Группа ресурсов | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Субъект-служба может __только__ регистрировать кластеры в группе ресурсов `myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Выходные данные:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Использование субъект-службы с Azure CLI

Ссылка на только что созданную субъект-службу:

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

* [Использование Политики Azure для управления конфигураций кластера](./use-azure-policy.md)
