---
title: Использование kubectl с Azure Dev Spaces | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: b0ceccc4f8b16c21e8a66a5f1b8cf0e7b6f47a4f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469317"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Использование kubectl с Azure Dev Spaces

В Azure Dev Spaces можно обращаться к кластеру Kubernetes и использовать существующие средства Kubernetes, например `kubectl`.

При выполнении команды `az aks use-dev-spaces` автоматически добавляется контекст конфигурации `kubectl`, поэтому средство kubectl уже должно быть подключено к кластеру Kubernetes в Azure Dev Spaces. Примеры:
- Подтверждение текущего контекста: `kubectl config current-context`.
- Вывод списка всех доступных контекстов: `kubectl config get-contexts`. 
- Изменение контекста: `kubectl config use-context <context-name>`.
- Просмотр панели мониторинга Kubernetes: выполните команду `kubectl proxy`, а затем откройте в браузере адрес, который создает эта команда (добавьте `/ui` к URL-адресу для перехода к панели мониторинга Kubernetes).
- Вывод списка запущенных служб в пространстве Azure Dev Spaces по умолчанию с именем *default*: `kubectl get services --namespace=default`.

