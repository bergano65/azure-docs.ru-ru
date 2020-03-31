---
title: Обзор консула
description: Получить обзор консула
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594215"
---
# <a name="consul"></a>Консул

## <a name="overview"></a>Обзор

[Consul][consul] — это многофункциональное сетевое решение центра обработки данных для подключения и обеспечения безопасности служб на платформах. [Connect][consul-features] — это компонент, предоставляющий возможности сетки обслуживания.

## <a name="architecture"></a>Architecture

Консул предоставляет плоскость данных, которая состоит из наосновенных [по умолчанию][envoy-proxy] [боков,][consul-sidecar] основанных на посланнике. Консул имеет pluggable прокси-архитектуры. Эти интеллектуальные прокси контролируют весь сетевой трафик в и из ваших сетчатых приложений и рабочих нагрузок.

Плоскость управления управляет конфигурацией и политикой через следующие [компоненты:][consul-architecture]

- **Server** - Консул-агент, работающий в режиме Сервера, который поддерживает состояние кластера Consul.

- **Клиент** - Консул Агент работает в легком режиме клиента. Каждый вычислительный узла должен иметь агента клиента. Эта конфигурация и политика клиентов-брокеров между рабочими нагрузками и конфигурацией Консула. 

Следующая диаграмма архитектуры показывает, как взаимодействуют различные компоненты в плоскости данных и плоскости управления.

![Обзор компонентов и архитектуры консула.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Критерии отбора

При оценке работы Консула важно понимать и учитывать следующие области:

- [Консульские принципы](#consul-principles)
- [Возможности](#capabilities)
- [Сценарии](#scenarios)


### <a name="consul-principles"></a>Консульские принципы

Проект [консула:][consul-principles]

- **API-Driven** - Кодифицировать всю конфигурацию и политику.

- **Выполнить и подключить в любом месте** - Подключение рабочих нагрузок через запуск платформ (Kubernetes, VMs, Без серверов).

- **Extend and Integrate** - Безопасное подключение рабочих нагрузок по инфраструктуре.


### <a name="capabilities"></a>Возможности

Консул предоставляет следующий набор возможностей:

- **Сетка** - шлюз (многофункциональный центр обработки данных), виртуальные машины (из кластерных узлов), синхронизация службы, встроенная в опцию отладки

- **Прокси** - Envoy, встроенный прокси, подключаемый, прокси l4, доступный для рабочих нагрузок Windows

- **Управление трафиком** - разгром, расщепление, разрешение

- **Политика** – намерения, ACLs

- **Безопасность** — авторизация, аутентификация, шифрование, идентификаторы на основе SPIFFE, внешние CA (Vault), управление сертификатами и ротация

- **Наблюдение** - метрики, панель мониторинга ui, прометей, графана


### <a name="scenarios"></a>Сценарии

Консул хорошо подходит и предлагается для следующих сценариев:

- Расширение существующих подключенных к Консулу рабочих нагрузок

- Требования к соблюдению требований в отношении управления сертификатами

- Многокластерная сетка обслуживания

- Рабочие нагрузки на основе VM, которые будут включены в сервисную сетку



## <a name="next-steps"></a>Дальнейшие действия

В следующей документации описывается, как можно установить консула на службе Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Установка консула в Azure Kubernetes Service (AKS)][consul-install]

Вы также можете дополнительно изучить особенности и архитектуру Консула:

- [Консул Особенности][consul-features]
- [Консул Архитектура][consul-architecture]
- [Консул - Как работает connect][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
