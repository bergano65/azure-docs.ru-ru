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
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052602"
---
# <a name="service-fabric-application-scenarios"></a>Сценарии приложений Service Fabric
Azure Service Fabric предоставляет надежную и гибкую платформу где можно писать и запускать множество типов бизнес-приложений и служб. Эти приложения и микрослужбы могут быть с отслеживанием или без отслеживания состояния, и они Балансировка ресурсов на виртуальных машинах для обеспечения максимальной эффективности. 

Уникальная архитектура структуры служб позволяет выполнять анализ данных в режиме, близком к режиму реального времени, вычисления в памяти, параллельные транзакции и обработку событий для ваших приложений. Вы можете без труда масштабировать (в большей степени горизонтально) приложения в зависимости от изменяющихся требований к ресурсам.

Руководство по проектированию на создании приложений, в статье [архитектура Микрослужб в Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) и [советы и рекомендации для разработки приложений с помощью Service Fabric](service-fabric-best-practices-applications.md).

Рассмотрите возможность использования платформы Service Fabric для следующих типов приложений:

* **Сбор данных, обработка и Интернет вещей**: Service Fabric обрабатывает большой объем и обеспечивает низкую задержку благодаря его служб с отслеживанием состояния. Это может помочь обработка данных на миллионах устройств, в которых совместно размещены данные для устройства и вычислительные ресурсы.

    Клиенты, которые создали служб Интернета вещей с помощью Service Fabric включают [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), и [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Интерактивных приложений, игр и на основе сеансов**: Service Fabric удобно в том случае, если приложению требуется низкую задержку операций чтения и записи, например в Интернет-игр или обмена мгновенными сообщениями. Service Fabric позволяет создавать эти интерактивные приложения с отслеживанием состояния без необходимости создавать отдельное хранилище или кэш. Посетите [обеспечивают игровые решения Azure](https://azure.microsoft.com/solutions/gaming/) инструкции по проектированию на [с помощью Service Fabric в службах игр](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Клиенты, которые создали игровые службы включают [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) и [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Клиенты, которые создали интерактивные сеансы включают [Honeywell с Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Анализ данных и обработки рабочего процесса**: Приложения, которые должны надежно обрабатывать события или потоки данных преимущества оптимизированный считывает и записывает в Service Fabric. Service Fabric также поддерживает конвейеры обработки приложения, в которых результаты должны быть надежными и передаваться на следующий этап обработки без потерь. Эти конвейеры относятся транзакционные и финансовые системы, где необходимы гарантии согласованности и вычисления данных.

    Клиенты, которые создали бизнес-служб рабочих процессов включают [группы Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [компания Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric), и [компании General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Вычисление данных**: Service Fabric позволяет создавать приложения с отслеживанием состояния, которые выполняют вычисления с большим объемом данных. Service Fabric позволяет совместное размещение, вычислительные и данных в приложениях. 

   Как правило если приложению требуется доступ к данным, сетевая задержка, связанная с внешних данных кэша или хранилище уровень, который ограничивает времени вычислений. Службы с отслеживанием состояния Service Fabric устранить эту задержку, позволяя оптимизировать чтение и запись. 
   
   Например рассмотрим приложение, выполняющее практически отбор рекомендаций в режиме реального времени для клиентов, с временем приема-передачи требование менее 100 миллисекунд. Характеристики задержки и производительности служб Service Fabric предоставляют более высокую скорость работы для пользователя, по сравнению с моделью Стандартная реализация того, чтобы получить необходимые данные из внешнего хранилища. Система еще удобнее, так как вычисление отбора рекомендаций не размещен совместно с данными и правилами.

    Клиенты, которые создали службы вычислений включают [ответа Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) и [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Высокодоступные службы**. Service Fabric обеспечивает быструю отработку отказа, создавая несколько вторичных реплик службы. В случае выхода из строя узла, процесса или отдельной службы по причине аппаратного или иного рода сбоя одна из вторичных реплик становится первичной при минимальном интервале прекращении обслуживания.

* **Масштабируемые службы**. Отдельные службы можно разделить на секции, чтобы можно было расширить их состояние в кластере. Отдельные службы можно создать и удалить в режиме реального времени. Горизонтальное масштабирование служб от нескольких экземпляров на нескольких узлах до тысяч экземпляров на множестве узлов и масштабировать их при необходимости. Service Fabric можно использовать для создания этих служб и управления ими полный жизненные циклы.

## <a name="application-design-case-studies"></a>Примеры проектов приложений
Примеры внедрения, в которых показано, как Service Fabric используется для проектирования приложений, опубликовано в [истории клиентов](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) и [Микрослужб в Azure](https://azure.microsoft.com/solutions/microservice-applications/) сайтов.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Проектирование приложений состоящие из микрослужб с отслеживанием и без отслеживания состояния
Создание приложений с помощью рабочих ролей облачных служб Azure является примером службы без отслеживания состояния. И наоборот, микрослужбы с отслеживанием состояния сохраняют утвержденное состояние за пределами запроса и ответа на него. Эта функция обеспечивает высокую доступность и согласованность состояния благодаря простым API, которые обеспечивают осуществление транзакций, поддерживаемое службой репликации. 

Службы с отслеживанием состояния в Service Fabric обеспечения высокой доступности для всех типов приложений, а не только баз данных и другие хранилища данных. Это логично. Разработчики приложений уже перешли от использования чисто реляционных баз данных для обеспечения высокой доступности к базам данных NoSQL. Теперь приложения могут организовать управление своим текущим состоянием и "горячими" данными внутри себя самих, что повышает производительность, не снижая надежности, целостности или доступности.

При создании приложения, состоящие из микрослужб, у вас обычно есть сочетание без отслеживания состояния веб-приложений (таких как ASP.NET и Node.js) вызывающего на службы без отслеживания состояния и с отслеживанием состояния бизнеса среднего уровня. Приложения и службы развертываются в одном кластере Service Fabric с помощью команд развертывания Service Fabric. Каждая из этих служб независима в плане масштабирования, надежности и использования ресурсов. Эта независимость повышает гибкость и гибкие возможности разработки и управления жизненным циклом.

Микрослужбы с отслеживанием состояния упрощают разработку приложений, поскольку устраняют необходимость в дополнительных очередях и кэше, которые традиционно требовались для обеспечения доступности соответствия требованиям по задержке для приложений, работающих только без отслеживания состояний. Так как службы с отслеживанием состояния имеют высокий уровень доступности и низкой задержкой, существует меньше сведений для управления в приложении. 

На следующей схеме показаны различия между проектированием приложения без отслеживания состояния и с отслеживанием состояния. Благодаря преимуществам моделей программирования [Reliable Services](service-fabric-reliable-services-introduction.md) и [Reliable Actors](service-fabric-reliable-actors-introduction.md) службы с отслеживанием состояния снижают сложность приложений, одновременно обеспечивая высокую пропускную способность и низкие значения задержки.

Ниже приведен пример приложения, использующего службы без отслеживания состояния: ![Приложения, использующего службы без отслеживания состояния][Image1]

Ниже приведен пример приложения, использующего службы с отслеживанием состояния: ![Приложения, использующего службы без отслеживания состояния][Image2]

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [шаблонами и сценариями](service-fabric-patterns-and-scenarios.md).

* Приступите к созданию службы без отслеживания состояния и с отслеживанием состояния с помощью Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) и [Reliable Actors](service-fabric-reliable-actors-get-started.md) модели программирования.
* Посетите Центр архитектуры Azure рекомендации на [создания микрослужб в Azure](https://docs.microsoft.com/azure/architecture/microservices/).
* Перейдите к [приложения Azure Service Fabric и кластером рекомендации](service-fabric-best-practices-overview.md) инструкции по проектированию приложения.

* Также ознакомьтесь со следующими темами:
  * [Расскажите мне о микрослужбах](service-fabric-overview-microservices.md)
  * [Определение состояния службы и управление им](service-fabric-concepts-state.md)
  * [Доступность служб структуры служб](service-fabric-availability-services.md)
  * [Масштабирование служб Service Fabric](service-fabric-concepts-scalability.md)
  * [Разделение служб Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
