---
title: Функции Azure в Kubernetes с использованием KEDA
description: Узнайте, как запуск функций Azure в Kubernetes в облаке или локально с помощью KEDA, автоматическое масштабирование на основе событий на основе Kubernetes.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure функции, функции, обработка событий, динамические вычисления, бессерверная архитектура, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077625"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Функции Azure в Kubernetes с использованием KEDA

Среда выполнения функций Azure предоставляет гибкие возможности размещения, где и как необходимо.  [KEDA](https://github.com/kedacore/kore) (на базе Kubernetes автомасштабирования на основе событий) в виде пары без проблем с среда выполнения функций Azure и средства, чтобы выполнять масштабирование на основе событий в Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Функции работой на базе Kubernetes

Служба "функции Azure" состоит из двух основных компонентов: среды выполнения и контроллер масштабирования.  Среда выполнения функций выполняется и выполнения кода.  Среда выполнения включает логику о том, как активировать, журнала и управлять выполнений этой функции.  Другим компонентом является контроллер масштабирования.  Контроллер масштабирования отслеживает частота возникновения событий, ориентированные на работу и заранее масштабирует количество экземпляров, в которых выполняется приложение.  Дополнительные сведения см. в разделе [масштабирование и размещение функций Azure](functions-scale.md).

Функции на основе Kubernetes предоставляет среда выполнения функций в [контейнера Docker](functions-create-function-linux-custom-image.md) с событиями масштабирования через KEDA.  KEDA можете уменьшить масштаб 0 экземпляры (если не возникает никаких событий) и до *n* экземпляров. Это достигается путем предоставления пользовательских метрик для автомасштабирования Kubernetes (по горизонтали автомасштабирования Pod).  Использование функции контейнеров с KEDA дает возможность реплицировать возможности независимой от сервера функции в любом кластере Kubernetes.  Эти функции также могут развертываться с помощью [виртуальные узлы службы Azure Kubernetes (AKS)](../aks/virtual-nodes-cli.md) функцию для независимой от сервера инфраструктурой.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Управление KEDA и функций в Kubernetes

Для выполнения функций в кластере Kubernetes, необходимо установить компонент KEDA. Установкой этого компонента используется [основных инструментов функций Azure](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Установка основных инструментов, с помощью функций Azure

По умолчанию основные инструменты устанавливает KEDA и Osiris компоненты, которые поддерживают управляемые событиями и масштабирование HTTP, соответственно.  В установке используется `kubectl` под управлением в текущем контексте.

Установите KEDA в кластере, выполнив следующие команды установки:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Развертывание приложения-функции в Kubernetes

Для кластера Kubernetes, работающего KEDA можно развернуть любые приложения-функции.  Так как функции выполняются в контейнере Docker, проекту требуется `Dockerfile`.  Если он еще не создан, можно добавить файл Dockerfile, выполнив следующую команду в корне проекта функций:

```cli
func init --docker-only
```

Для создания образа и развертывания своих функций в Kubernetes, выполните следующую команду:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Замените `<name-of-function-deployment>` на имя приложения-функции.

При этом создается Kubernetes `Deployment` ресурсов, `ScaledObject` ресурсов, и `Secrets`, который включает в себя переменные среды, импортированных из вашей `local.settings.json` файл.

## <a name="removing-a-function-app-from-kubernetes"></a>Удаление приложения-функции с помощью Kubernetes

После развертывания вы можно удалить функцию путем удаления связанного `Deployment`, `ScaledObject`, `Secrets` создан.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Удаление KEDA с Kubernetes

Можно выполните следующую команду средства core удаляемый KEDA из кластера Kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Поддерживаемые триггеров в KEDA

KEDA в данный момент бета-версии с поддержкой следующих триггеры функций Azure:

* [Очереди хранилища Azure](functions-bindings-storage-queue.md)
* [Очереди шины обслуживания Azure](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Следующие шаги
Для получения дополнительных сведений см. следующие ресурсы:

* [Создание функции с помощью пользовательского образа](functions-create-function-linux-custom-image.md)
* [Как программировать и тестировать функции Azure в локальной среде](functions-develop-local.md)
* [Как работает план потребления функций Azure](functions-scale.md)