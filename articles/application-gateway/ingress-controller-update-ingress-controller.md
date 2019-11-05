---
title: Обновление входящего контроллера с помощью Helm
description: В этой статье содержатся сведения об обновлении входящего шлюза приложений с помощью Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 6ed73a2172e09e7255447b4467698670c95b63af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513241"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Обновление входящего контроллера шлюза приложений с помощью Helm 

Контроллер входящего трафика шлюза приложений Azure для Kubernetes (АГИК) можно обновить с помощью репозитория Helm, размещенного в службе хранилища Azure.

Прежде чем начать процедуру обновления, убедитесь, что добавлен необходимый репозиторий:

- Просмотрите добавленные в настоящее время репозитории Helm с помощью:

    ```bash
    helm repo list
    ```

- Добавьте репозиторий АГИК с:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Обновление

1. Обновите репозиторий АГИК Helm, чтобы получить последний выпуск:

    ```bash
    helm repo update
    ```

1. Просмотр доступных версий `application-gateway-kubernetes-ingress`ной диаграммы:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Пример ответа:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Последняя доступная версия из приведенного выше списка: `0.7.0-rc1`

1. Просмотр установленных на данный момент диаграмм Helm:

    ```bash
    helm list
    ```

    Пример ответа:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Установка диаграммы Helm из примера приведенного выше ответа называется `odd-billygoat`. Мы будем использовать это имя для остальных команд. Действительное имя развертывания, скорее всего, будет отличаться.

1. Обновите развертывание Helm до новой версии:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Откат

Если развертывание Helm завершится сбоем, можно вернуться к предыдущему выпуску.

1. Получите последний известный рабочий номер выпуска:

    ```bash
    helm history odd-billygoat
    ```

    Пример выходных данных:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    В примере выходных данных команды `helm history` он выглядит так, как Последнее успешное развертывание нашей `odd-billygoat` имело значение Revision `1`

1. Откат к последней успешной редакции:

    ```bash
    helm rollback odd-billygoat 1
    ```
