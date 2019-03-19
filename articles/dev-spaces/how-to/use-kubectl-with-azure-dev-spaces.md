---
title: Как использовать kubectl с пробелами разработки Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: 'Docker, Kubernetes, Azure, AKS, служба Azure Kubernetes, контейнеры, Helm, слой взаимодействия между службами, служба маршрутизации сетки, kubectl, k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773271"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Использование kubectl с Azure Dev Spaces

В Azure Dev Spaces можно обращаться к кластеру Kubernetes и использовать существующие средства Kubernetes, например `kubectl`.

При выполнении команды `az aks use-dev-spaces` автоматически добавляется контекст конфигурации `kubectl`, поэтому средство kubectl уже должно быть подключено к кластеру Kubernetes в Azure Dev Spaces. Примеры:
- Подтверждение текущего контекста: `kubectl config current-context`.
- Вывод списка всех доступных контекстов: `kubectl config get-contexts`. 
- Изменение контекста: `kubectl config use-context <context-name>`.
- Просмотр панели мониторинга Kubernetes: выполните команду `kubectl proxy`, а затем откройте в браузере адрес, который создает эта команда (добавьте `/ui` к URL-адресу для перехода к панели мониторинга Kubernetes).
- Вывод списка запущенных служб в пространстве Azure Dev Spaces по умолчанию с именем *default*: `kubectl get services --namespace=default`.

