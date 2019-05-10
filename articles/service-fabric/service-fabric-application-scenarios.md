---
title: Сценарии приложений и разработка | Документация Майкрософт
description: Общие сведения о категориях облачных приложений в Service Fabric. Рассматривается разработка приложений, использующих службы с отслеживанием состояния и без него.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228497"
---
# <a name="service-fabric-application-scenarios"></a>Сценарии приложений Service Fabric
Azure Service Fabric — это надежная и гибкая платформа, позволяющая создавать и запускать множество типов бизнес-приложений и служб. Эти приложения и микрослужбы могут регистрировать или не регистрировать состояния, при этом для них выполняется балансировка ресурсов на используемых виртуальных машинах для обеспечения максимальной эффективности. Уникальная архитектура структуры служб позволяет выполнять анализ данных в режиме, близком к режиму реального времени, вычисления в памяти, параллельные транзакции и обработку событий для ваших приложений. Вы можете без труда масштабировать (в большей степени горизонтально) приложения в зависимости от изменяющихся требований к ресурсам.

Руководство по проектированию на создании приложений, в статье [архитектура микрослужб в Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) и [советы и рекомендации для разработки приложений с помощью Service Fabric](service-fabric-best-practices-applications.md)

Платформа Service Fabric идеально подходит для следующих типов приложений:

* **Сбор данных, обработка и Интернет вещей**: Так как Service Fabric обрабатывает большой объем и обеспечивает низкую задержку благодаря его служб с отслеживанием состояния, он идеально подходит для обработки данных на миллионах устройств которых совместно размещены данные для устройства и вычислительные ресурсы.

    Клиенты, которые создали служб Интернета вещей, с помощью Service Fabric включают [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), и [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Интерактивных приложений, игр и на основе сеансов**: Service Fabric идеально, если приложению требуется низкой задержки операций чтения и записи, например в Интернет-игр или обмена мгновенными сообщениями. Service Fabric позволяет создавать эти интерактивные приложения с отслеживанием состояния без необходимости создавать отдельное хранилище или кэш. Посетите [обеспечивают игровые решения Azure](https://azure.microsoft.com/solutions/gaming/) инструкции по проектированию на [в службах игр с помощью Service Fabric](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Клиенты, которые создали игровые службы включают [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) и [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Клиенты, которые создали интерактивные сеансы включают [Honeywell с Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Анализ данных и обработки рабочего процесса**: Приложения, которые должны надежно обрабатывать события или потоки данных также преимущество из оптимизированного операций чтения и записи в Service Fabric. Кроме того, Service Fabric поддерживает конвейеры обработки приложения, в которых результаты должны быть надежными и передаваться на следующий этап обработки без потерь. К ним относятся транзакционные и финансовые системы, где важно гарантировать согласованность и надлежащее вычисление данных.

    Клиенты, которые создали бизнес-служб рабочих процессов включают [группы Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) и [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Вычисление данных**: Service Fabric позволяет создавать приложения с большим объемом с отслеживанием состояния вычисления данных. Service Fabric позволяет совместное размещение данных и обработки (вычисления) в приложениях. Как правило если приложению требуется доступ к данным, сетевая задержка, связанная с внешних данных кэша или хранилище уровень, который ограничивает времени вычислений. Служб с отслеживанием состояния Service Fabric что задержка исключается, включение более оптимизированных для операций чтения и записи. Например рассмотрим приложение, выполняющее практически отбор рекомендаций в режиме реального времени для клиентов, с временем приема-передачи требование менее 100 миллисекунд. Характеристики задержки и производительности служб Service Fabric (где вычисление отбора рекомендаций не размещен совместно с данными и правилами) обеспечивает более высокую скорость работы для пользователя, по сравнению с моделью стандартной реализации При необходимости получить необходимые данные из внешнего хранилища.

    Клиенты, которые создали службы вычислений включают [ответа Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) и [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Высокодоступные службы**. Service Fabric обеспечивает быструю отработку отказа, создавая несколько вторичных реплик службы. В случае выхода из строя узла, процесса или отдельной службы по причине аппаратного или иного рода сбоя одна из вторичных реплик становится первичной при минимальном интервале прекращении обслуживания.

* **Масштабируемые службы**. Отдельные службы можно разделить на секции, чтобы можно было расширить их состояние в кластере. Кроме того, вы можете создавать и удалять отдельные службы в режиме реального времени. Службы могут быстро и легко быть масштабировать от нескольких экземпляров на нескольких узлах до тысяч экземпляров на множестве узлов, а затем масштабированием в зависимости от потребностей в ресурсах. Service Fabric можно использовать для создания этих служб и управления ими полный жизненные циклы.

## <a name="application-design-case-studies"></a>Примеры проектов приложений
Несколько примеров, показывающих, как Service Fabric используется для проектирования приложений, опубликовано на [истории клиентов](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) и [сайте решений микрослужб](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Разработка приложений, состоящих из микрослужб с отслеживанием и без отслеживания состояния
Создание приложений с ролями Worker в облачной службе Azure является примером службы без отслеживания состояния. И наоборот, микрослужбы с отслеживанием состояния сохраняют утвержденное состояние за пределами запроса и ответа на него. Эта функция обеспечивает высокую доступность и согласованность состояния благодаря простым API, которые обеспечивают осуществление транзакций, поддерживаемое службой репликации. Службы Service Fabric с отслеживанием состояния делают высокую доступность "общедоступной" для всех типов приложений, а не только для баз данных и других хранилищ данных. Это логично. Разработчики приложений уже перешли от использования чисто реляционных баз данных для обеспечения высокой доступности к базам данных NoSQL. Теперь приложения могут организовать управление своим текущим состоянием и "горячими" данными внутри себя самих, что повышает производительность, не снижая надежности, целостности или доступности.

При создании приложений, состоящих из микрослужб, у вас обычно есть ряд веб-приложений без отслеживания состояния (ASP.NET, Node.js и т. п.), вызывающих службы среднего уровня как с отслеживанием состояния, так и без него. Эти службы развернуты в одном кластере Service Fabric с помощью команд развертывания Service Fabric. Каждая из этих служб независима в плане масштабирования, надежности и использования ресурсов, что значительно повышает гибкость и гибкие возможности разработки и управления жизненным циклом.

Микрослужбы с отслеживанием состояния упрощают разработку приложений, поскольку устраняют необходимость в дополнительных очередях и кэше, которые традиционно требовались для обеспечения доступности соответствия требованиям по задержке для приложений, работающих только без отслеживания состояний. Поскольку служб с отслеживанием состояния, естественным образом, имеют высокий уровень доступности и низкой задержкой, существуют меньшее количество перемещаемых элементов для управления в приложении в целом. На диаграммах ниже представлены различия между проектированием приложения без отслеживания состояния и с отслеживанием состояния. Благодаря преимуществам моделей программирования [Reliable Services](service-fabric-reliable-services-introduction.md) и [Reliable Actors](service-fabric-reliable-actors-introduction.md) службы с отслеживанием состояния снижают сложность приложений, одновременно обеспечивая высокую пропускную способность и низкие значения задержки.

## <a name="an-application-built-using-stateless-services"></a>Приложение, созданное при помощи служб без отслеживания состояния
![Приложение, использующее службу без отслеживания состояния][Image1]

## <a name="an-application-built-using-stateful-services"></a>Приложение, созданное при помощи служб с отслеживанием состояния
![Приложение, использующее службу без отслеживания состояния][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с [примерами реальных клиентов](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc).
* Дополнительные сведения о [шаблонах и сценариях](service-fabric-patterns-and-scenarios.md).

* Начните создавать службы с отслеживанием состояния и без него, используя модели программирования [Reliable Services](service-fabric-reliable-services-quick-start.md) и [Reliable Actors](service-fabric-reliable-actors-get-started.md), включенные в Service Fabric.
* Посетите Центр архитектуры Azure рекомендации на [создания микрослужб в Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* Перейдите к [приложения Azure Service Fabric и кластером рекомендации](service-fabric-best-practices-overview.md) инструкции по проектированию приложения.

* Также ознакомьтесь со следующими темами:
  * [Расскажите мне о микрослужбах](service-fabric-overview-microservices.md)
  * [Определение состояния службы и управление им](service-fabric-concepts-state.md)
  * [Доступность служб структуры служб](service-fabric-availability-services.md)
  * [Масштабирование служб Service Fabric](service-fabric-concepts-scalability.md)
  * [Разделение служб Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
