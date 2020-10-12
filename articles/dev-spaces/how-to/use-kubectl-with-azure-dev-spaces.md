---
title: Использование kubectl с Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Узнайте, как использовать команды kubectl в пространстве разработки в кластере службы Kubernetes Azure с включенным Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75438366"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Использование kubectl с Azure Dev Spaces

В Azure Dev Spaces можно обращаться к кластеру Kubernetes и использовать существующие средства Kubernetes, например `kubectl`.

При выполнении команды `az aks use-dev-spaces` автоматически добавляется контекст конфигурации `kubectl`, поэтому средство kubectl уже должно быть подключено к кластеру Kubernetes в Azure Dev Spaces. Примеры:
- Подтверждение текущего контекста: `kubectl config current-context`.
- Вывод списка всех доступных контекстов: `kubectl config get-contexts`. 
- Изменение контекста: `kubectl config use-context <context-name>`.
- Просмотр панели мониторинга Kubernetes: выполните команду `kubectl proxy`, а затем откройте в браузере адрес, который создает эта команда (добавьте `/ui` к URL-адресу для перехода к панели мониторинга Kubernetes).
- Перечислите выполняющиеся службы в Azure Dev Spacesном пространстве по умолчанию с именем *по умолчанию*: `kubectl get services --namespace=default`

