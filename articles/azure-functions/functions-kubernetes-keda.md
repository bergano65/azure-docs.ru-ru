---
title: Функции Azure в Kubernetes с Кеда
description: Узнайте, как выполнять функции Azure в Kubernetes в облаке или локальной среде с помощью Кеда, управляемого событиями автоматического масштабирования на основе Kubernetes.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: функции Azure, функции, обработка событий, Динамическое вычисление, бессерверная архитектура, kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096096"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Функции Azure в Kubernetes с Кеда

Среда выполнения функций Azure обеспечивает гибкость при размещении, где и как вам нужно.  [Кеда](https://github.com/kedacore/kore) (Автоматическое масштабирование, управляемое событиями на основе Kubernetes), легко с помощью среды выполнения функций Azure и инструментария для обеспечения масштабирования на основе событий в Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Принципы работы функций на основе Kubernetes

Служба "функции Azure" состоит из двух ключевых компонентов: среды выполнения и контроллера масштабирования.  Среда выполнения функций выполняет и выполняет код.  Среда выполнения включает в себя логику для активации, ведения журнала и управления выполнением функций.  Другой компонент является контроллером масштабирования.  Контроллер масштабирования отслеживает скорость событий, предназначенных для функции, и заранее масштабирует количество экземпляров, в которых выполняется приложение.  Дополнительные сведения см. в статье [Масштабирование и размещение Функций Azure](functions-scale.md).

Функции на основе Kubernetes предоставляют среду выполнения функций в [контейнере DOCKER](functions-create-function-linux-custom-image.md) с масштабированием на основе событий через Кеда.  Кеда может масштабироваться до 0 экземпляров (если события не происходят) и до *n* экземпляров. Это достигается за счет предоставления пользовательских метрик для автомасштабирования Kubernetes (горизонтальный Автомасштабирование Pod).  Использование контейнеров функций с Кеда позволяет реплицировать функции бессерверных функций в любом кластере Kubernetes.  Эти функции также можно развернуть с помощью [виртуальных узлов Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) для бессерверной инфраструктуры.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Управление Кеда и функциями в Kubernetes

Для запуска функций в кластере Kubernetes необходимо установить компонент Кеда. Этот компонент можно установить с помощью [Azure functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Установка с помощью Azure Functions Core Tools

По умолчанию основные инструменты устанавливают компоненты Кеда и Осирис, которые поддерживают масштабирование на основе событий и HTTP соответственно.  В текущем контексте `kubectl` используется выполняемая установка.

Установите Кеда в кластере, выполнив следующую команду установки:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Развертывание приложения-функции в Kubernetes

Вы можете развернуть любое приложение-функцию в кластере Kubernetes, работающем под Кеда.  Так как функции выполняются в контейнере DOCKER, вашему проекту требуется `Dockerfile`.  Если у вас ее еще нет, можно добавить Dockerfile, выполнив следующую команду в корне проекта функций:

```cli
func init --docker-only
```

Чтобы создать образ и развернуть функции в Kubernetes, выполните следующую команду:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Замените `<name-of-function-deployment>` на имя приложения-функции.

При этом создается ресурс `Deployment` Kubernetes `ScaledObject` , ресурс и `Secrets` `local.settings.json` , который включает переменные среды, импортированные из файла.

## <a name="removing-a-function-app-from-kubernetes"></a>Удаление приложения-функции из Kubernetes

После развертывания можно удалить функцию, удалив связанную `Deployment`, `ScaledObject` `Secrets` созданную.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Удаление Кеда из Kubernetes

Чтобы удалить Кеда из кластера Kubernetes, выполните следующую команду Core Tools:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Поддерживаемые триггеры в Кеда

В настоящее время бета-версия Кеда поддерживает следующие триггеры функций Azure:

* [Очереди службы хранилища Azure](functions-bindings-storage-queue.md)
* [Очереди служебной шины Azure](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Следующие шаги
Для получения дополнительных сведений см. следующие ресурсы:

* [Создание функции с помощью пользовательского образа](functions-create-function-linux-custom-image.md)
* [Как программировать и тестировать функции Azure в локальной среде](functions-develop-local.md)
* [Как работает план потребления функций Azure](functions-scale.md)