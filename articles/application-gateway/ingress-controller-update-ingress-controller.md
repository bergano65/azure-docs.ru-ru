---
title: Обновление всхотливый контроллер с хелмом
description: В этой статье содержится информация о том, как обновить шлюз приложения Ingress с помощью Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795903"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>How to upgrade Application Gateway Ingress Controller using Helm (Обновление контроллера входящего трафика Шлюза приложений с помощью Helm) 

Контроллер входа в приложение Azure для Kubernetes (AGIC) можно обновить с помощью репозитория Helm, размещенного на хранилище Azure.

Прежде чем приступить к процедуре обновления, убедитесь, что вы добавили необходимый репозиторий:

- Просмотр репозиториев helm, которые в настоящее время добавлены, с помощью:

    ```bash
    helm repo list
    ```

- Добавить РЕПО AGIC с:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Обновление

1. Освежите репозиторий AGIC Helm, чтобы получить последний релиз:

    ```bash
    helm repo update
    ```

1. Просмотр доступных `application-gateway-kubernetes-ingress` версий диаграммы:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Пример ответа:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Последняя доступная версия из приведенного выше списка:`0.7.0-rc1`

1. Просмотр установленных в настоящее время диаграмм helm:

    ```bash
    helm list
    ```

    Пример ответа:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Установка диаграммы Helm из приведенного `odd-billygoat`выше ответа на образец названа. Мы будем использовать это имя для остальных команд. Ваше имя развертывания, скорее всего, будет отличаться.

1. Обновление развертывания Helm до новой версии:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Откат

Если развертывание Helm не удается, можно откатить к предыдущему выпуску.

1. Получите последний известный здоровый номер релиза:

    ```bash
    helm history odd-billygoat
    ```

    Образец вывода:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Из образца вывода `helm history` команды это выглядит как последнее успешное развертывание нашего `odd-billygoat` был пересмотр`1`

1. Откат к последнему успешному пересмотру:

    ```bash
    helm rollback odd-billygoat 1
    ```
