---
title: RBAC roles and permissions
description: Используйте управление доступом Azure на основе ролей (RBAC) и управление удостоверениями и доступом (IAM) для предоставления детализированных разрешений для доступа к ресурсам в реестре контейнеров Azure.
ms.topic: article
ms.date: 03/20/2019
ms.openlocfilehash: 8ef4f26dfd59c7b3b177ef58fa23e08f7e66d328
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456243"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Роли и разрешения реестра контейнеров Azure

Служба реестра контейнеров Azure поддерживает ряд Azure-ролей, которые предоставляют различные уровни разрешений для реестра контейнеров Azure. Используйте [управление доступом Azure на основе ролей (RBAC)](../role-based-access-control/index.yml), чтобы назначать пользователям или субъектам-службам, которые должны взаимодействовать с реестром, особые разрешения.

| Роль или разрешение       | [Доступ к Resource Manager](#access-resource-manager) | [Создание и удаление реестра](#create-and-delete-registry) | [Отправка образа](#push-image) | [Получение образа](#pull-image) | [Delete image data](#delete-image-data) | [Изменение политик](#change-policies) |   [Подписывание образов](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Владелец. | X | X | X | X | X | X |  |  
| участник; | X | X | X |  X | X | X |  |  
| Читатель | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Различие пользователей и служб

Каждый раз при применении разрешений рекомендуется задавать для пользователя или службы минимальный набор разрешений для выполнения задачи. Следующие наборы разрешений представляют наборы возможностей, которые могут использоваться пользователями и автономными службами.

### <a name="cicd-solutions"></a>Решения CI/CD

При автоматизации команд `docker build` от решений CI/CD вам необходимы возможности `docker push`. Для сценариев с автономными службами рекомендуем назначить роль **AcrPush**. Эта роль, в отличие от более широкой роли **Участник**, не позволяет учетной записи выполнять другие операции с реестром и обращаться к Azure Resource Manager.

### <a name="container-host-nodes"></a>Узлы контейнеров

Точно так же узлам, в которых запущены контейнеры, необходима роль **AcrPull**, но им не требуются возможности роли **Читатель**.

### <a name="visual-studio-code-docker-extension"></a>Расширение Docker для Visual Studio Code

Для таких средств, как [расширение Docker для Visual Studio Code](https://code.visualstudio.com/docs/azure/docker), необходим доступ к дополнительному поставщику ресурсов для получения доступных реестров контейнеров Azure. В этом случае предоставьте своим пользователям доступ к роли **Читатель** или **Участник**. Эти роли позволяют использовать `docker pull`, `docker push`, `az acr list`, `az acr build` и другие возможности. 

## <a name="access-resource-manager"></a>Доступ к Resource Manager

Доступ к Azure Resource Manager является обязательным для управления порталом Azure и реестром с помощью [Azure CLI](/cli/azure/). Это набор разрешений потребуется, например, чтобы получить список реестров с помощью команды `az acr list`. 

## <a name="create-and-delete-registry"></a>Создание и удаление реестра

Возможность создавать и удалять реестры контейнеров Azure.

## <a name="push-image"></a>Отправка образа

Возможность `docker push` образа или отправки другого [поддерживаемого артефакта](container-registry-image-formats.md), например диаграммы Helm, в реестр. Требуется [проверка подлинности](container-registry-authentication.md) в реестре с помощью авторизованного удостоверения. 

## <a name="pull-image"></a>Получение образа

Возможность `docker pull` образа, не находящегося на карантине, или получения другого [поддерживаемого артефакта](container-registry-image-formats.md), например диаграммы Helm, из реестра. Требуется [проверка подлинности](container-registry-authentication.md) в реестре с помощью авторизованного удостоверения.

## <a name="delete-image-data"></a>Удаление данных образа

The ability to [delete container images](container-registry-delete.md), or delete other [supported artifacts](container-registry-image-formats.md) such as Helm charts, from a registry.

## <a name="change-policies"></a>Изменение политик

Возможность настройки политик в реестре. Политики включают очистку образов, включение карантина и подписывание образов.

## <a name="sign-images"></a>Подписывание образов

Возможность подписывания образов обычно назначается автоматизированному процессу, который использует субъект-службу. Это разрешение обычно объединяется с разрешением на [отправку образа](#push-image), чтобы разрешить отправку доверенного образа в реестр. Дополнительные сведения см. в разделе [Доверие содержимому в реестре контейнеров Azure](container-registry-content-trust.md).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с дополнительными сведениями о назначении ролей RBAC для удостоверений Azure с помощью [портала Azure](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md) или других средств Azure.

* Ознакомьтесь с дополнительными сведениями о [вариантах проверки подлинности](container-registry-authentication.md) для Реестра контейнеров Azure.
