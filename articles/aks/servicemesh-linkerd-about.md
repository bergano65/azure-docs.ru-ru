---
title: Обзор Линкерда
description: Получить обзор Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593773"
---
# <a name="linkerd"></a>Линкерд

## <a name="overview"></a>Обзор

[Linkerd][linkerd] является простой в использовании и легкой сеткой обслуживания.

## <a name="architecture"></a>Architecture

Linkerd предоставляет плоскость данных, которая состоит из сверхлегких специализированных прокси-каров [Linker.][linkerd-proxy] Эти интеллектуальные прокси контролируют весь сетевой трафик в и из ваших сетчатых приложений и рабочих нагрузок. Прокси также разоблачают метрики через конечные точки [прометея.][prometheus]

Плоскость управления управляет конфигурацией и агрегированной телеметрией через следующие [компоненты:][linkerd-architecture]

- **Контроллер** - Предоставляет api, который управляет Linkerd CLI и приборной панелью. Обеспечивает конфигурацию для прокси- и прокси.

- **Нажмите** - Установите часы в режиме реального времени по запросам и ответам.

- **Identity** - Обеспечивает возможности идентификации и безопасности, которые позволяют mTLS между службами.

- **Web** - Обеспечивает панель мониторинга Linkerd.


Следующая диаграмма архитектуры показывает, как взаимодействуют различные компоненты в плоскости данных и плоскости управления.


![Обзор компонентов и архитектуры Linkerd.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Критерии отбора

При оценке Linkerd для рабочих нагрузок важно понимать и учитывать следующие области:

- [Принципы проектирования](#design-principles)
- [Возможности](#capabilities)
- [Сценарии](#scenarios)


### <a name="design-principles"></a>Принципы проектирования

Следующие принципы проектирования [определяют][design-principles] проект Linkerd:

- **Держите его простым** - должно быть простым в использовании и понять.

- **Минимизация требований к ресурсам** - Наложите минимальную производительность и затраты на ресурсы.

- **Just Work** - Не нарушайте существующие приложения и не требуйте сложной конфигурации.


### <a name="capabilities"></a>Возможности

Linkerd предоставляет следующий набор возможностей:

- **Сетка** - встроенный в опцию отладки

- **Управление трафиком** - разделение, тайм-ауты, повторы, вход

- **Безопасность** - шифрование (mTLS), сертификаты автоматически отменяются каждые 24 часа

- **Наблюдение** - золотые метрики, кран, трассировка, профили обслуживания и метрики маршрута, веб-панель с топологией графиков, прометей, графана


### <a name="scenarios"></a>Сценарии

Linkerd хорошо подходит и предлагается для следующих сценариев:

- Простой в использовании только с основным набором требований к возможностям

- Низкая задержка, низкие накладные расходы, с акцентом на наблюдаемость и простое управление движением


## <a name="next-steps"></a>Дальнейшие действия

В следующей документации описывается, как можно установить Linkerd на сервис Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Установка Linkerd в azure Kubernetes Service (AKS)][linkerd-install]

Вы также можете дополнительно изучить функции и архитектуру Linkerd:

- [Особенности Linkerd][linkerd-features]
- [Архитектура Линкерда][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md