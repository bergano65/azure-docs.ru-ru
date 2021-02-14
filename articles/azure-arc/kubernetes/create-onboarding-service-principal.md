---
title: Создание субъекта-службы адаптации с поддержкой дуги Azure (Предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Создание субъекта-службы адаптации с поддержкой дуги Azure '
keywords: Kubernetes, Arc, Azure, контейнеры
ms.openlocfilehash: 8772cf7634d9a833af120784e3e7868b41d202c4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390493"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Создание субъекта-службы адаптации с поддержкой дуги Azure (Предварительная версия)

## <a name="overview"></a>Обзор

Вы можете подключить кластеры Kubernetes к службе "Дуга Azure" с помощью субъектов-служб с назначениями ролей с ограниченными правами доступа. Эта возможность полезна в конвейерах непрерывной интеграции и непрерывного развертывания (CI/CD), таких как действия Azure Pipelines и GitHub.

Выполните следующие действия, чтобы узнать, как использовать субъекты-службы для адаптации кластеров Kubernetes к службе "Дуга" Azure.

## <a name="create-a-new-service-principal"></a>Создание нового субъекта-службы

Создайте новый субъект-службу с информативным именем, уникальным для клиента Azure Active Directory.

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

Назначьте только что созданному субъекту-службе роль "кластер Kubernetes — Azure Arc". Эта встроенная роль Azure с ограниченными разрешениями позволяет субъекту только регистрировать кластеры в Azure. Участник с этой назначенной ролью не может обновлять, удалять или изменять другие кластеры или ресурсы в подписке.

Учитывая ограниченные возможности, клиенты могут легко повторно использовать этот субъект для подключения нескольких кластеров.

Вы можете дополнительно ограничить разрешения, передав соответствующий `--scope` аргумент при назначении роли. Это позволяет клиентам ограничивать регистрацию в кластере. Следующие сценарии поддерживаются различными параметрами `--scope`:

| Ресурс  | Аргумент `scope`| Действие |
| ------------- | ------------- | ------------- |
| Подписка | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Субъект-служба может зарегистрировать любой кластер в существующей группе ресурсов в данной подписке. |
| Группа ресурсов | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Субъект-служба может регистрировать кластеры __только__ в группе ресурсов `myGroup` . |

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

## <a name="use-service-principal-with-the-azure-cli"></a>Использование субъекта-службы с Azure CLI

Сослаться на только что созданный субъект-службу с помощью следующих команд:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

* [Использование Политики Azure для управления конфигураций кластера](./use-azure-policy.md)
