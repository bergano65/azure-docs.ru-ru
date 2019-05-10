---
title: Azure Service Fabric разработки рекомендации приложений | Документация Майкрософт
description: Рекомендации по разработке приложений Service Fabric.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237752"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric разработки рекомендации приложений

Эта статья содержит рекомендации по созданию приложений и служб в Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Ознакомиться с Service Fabric
* [Таким образом вы хотите узнать о Service Fabric?](service-fabric-content-roadmap.md)
* Узнайте о [сценарии приложений Service Fabric](service-fabric-application-scenarios.md)
* Затем понять программирования модели выбора с [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Руководство по проектированию приложений
Ознакомиться с [общей архитектуре](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) приложения Service Fabric и его [рекомендации по проектированию](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Выберите шлюз API
Используйте службу шлюза API, который взаимодействует с серверными службами, которые можно легко масштабировать. Ниже приведены наиболее распространенные службы шлюза API, используемые.

- [Служба управления API Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), который является [интегрируется с Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Центр Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/) или [концентраторов событий](https://docs.microsoft.com/azure/event-hubs/) с помощью [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) для чтения из секции концентратора событий
- [Træfik обратный прокси-сервер](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) с помощью [поставщик Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Шлюз приложений Azure](https://docs.microsoft.com/azure/application-gateway/) Примечание: это не интегрируется непосредственно с Service Fabric и управления API Azure обычно является предпочтительным вариантом
- Создавайте свои собственные [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) веб-приложения шлюза

### <a name="choose-stateless-services"></a>Выберите службы без отслеживания состояния
Мы рекомендуем всегда запускать при создании службы без отслеживания состояния с помощью [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) сохранение состояния в в базе данных Azure, Azure CosmosDB или хранилище Azure. Наличие состояние выразили знакомую подход для большинства разработчиков и позволяет воспользоваться преимуществами возможности запросов в хранилище.  

### <a name="when-to-choose-stateful-services"></a>Случаи использования служб с отслеживанием состояния
Если сценарий для обеспечения минимальной задержки и должны хранить данные близко к вычислительных ресурсов, рассмотрите возможность служб с отслеживанием состояния. Некоторые примеры включают цифровой двойник устройства Интернета вещей, состояния игры, состояния сеанса, кэширование данных из базы данных и долго выполняющиеся рабочие процессы для отслеживания вызовов к другим службам.

Решите, интервала времени хранения данных:

- Кэшированные данные - использовании кэширования где задержка внешнем хранилище, является проблемой. Используйте службы с отслеживанием состояния, как данные кэша или рассмотрите возможность использования [открытым SoCreate service-fabric распределенных cache](https://github.com/SoCreate/service-fabric-distributed-cache). В этом случае вы можете потерять все данные в кэше, и он не имеет значения.
- Данные привязанный ко времени — вам нужно хранить данные рядом для вычисления задержки в течение заданного времени, но эти данные могут позволить себе потеряна при *аварийного* сценария. Например во многих решениях Интернета вещей, которые данных должно быть близко к вычислений, например Вычисление средней температуры за последние несколько дней, однако если эти данные в случае утери затем определенные точки данных записаны не важных. Также в этом сценарии вы не важна обычно резервной копии отдельных точек данных, только вычисляемые значения, которые периодически записываются во внешнее хранилище.  
- Долгосрочного хранения данных — надежные коллекции данных позволяет хранить их без возможности восстановления. Однако в этом случае необходимо [Подготовка к аварийному восстановлению](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) включая [Настройка периодического резервного копирования политик](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) для кластеров. По сути выполняется настройка, что происходит при удалении кластера в сценарии аварийного, где необходимо создать новый кластер и способ развертывания новых экземпляров приложения и восстановить из последней резервной копии.

Сократить расходы и повысить уровень доступности:
- Можно сократить расходы служб с отслеживанием состояния, так как не взимается, затрат на доступ и транзакции данных из удаленного хранилища, и нет необходимости использовать другой службы, например Redis для Azure.
- Использование служб с отслеживанием состояния, главным образом для хранилища, а не за вычислительные ресурсы — дорогостоящий и не рекомендуется. Службы с отслеживанием состояния можно считать вычислительных ресурсов с помощью недорогих локального хранилища.
- Путем удаления зависимости от других служб, можно повысить доступность службы. Того состояния, управляемого с высоким уровнем ДОСТУПНОСТИ в кластере, можно изолировать от других время простоя службы или проблем с задержкой.

## <a name="how-to-properly-work-with-reliable-services"></a>Как правильно работать со службами Reliable Services
Надежные службы позволяют легко создавать службы с отслеживанием и без отслеживания состояния. Чтение [Обзор надежных служб](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Всегда учитывает [токен отмены](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) в `RunAsync()` метод для служб без отслеживания состояния и с отслеживанием состояния и `ChangeRole()` метод для служб с отслеживанием состояния. Без этого Service Fabric не знает, если служба может быть закрыт. Например не учитывает маркер отмены, может привести к гораздо более длительное время обновления приложения.
-   Открывающие и закрывающие [прослушивателей связи](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) своевременно и могут получать токены отмены.
-   Запрещается совместное использование кода синхронизации с помощью асинхронного кода. Например, не используйте `.GetAwaiter().GetResult()` в асинхронных вызовах; оно должно быть async *вплоть* через стек вызовов.

## <a name="how-to-properly-work-with-reliable-actors"></a>Как правильно работать с Reliable Actors
Reliable Actors позволяет легко создавать субъекты с отслеживанием состояния, виртуальный. Чтение [Общие сведения о надежных субъектах](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Настоятельно рекомендуется использовать pub/sub, обмен сообщениями между субъекты для увеличения размера приложения. Например [открытым SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/) или [служебной шины Azure](https://docs.microsoft.com/azure/service-bus/).
- Состояния субъекта, как сделать [детализированные максимально](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Управление [жизненным циклом субъекта](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Удалите субъекты, если вы не собираетесь использовать их, когда-нибудь еще раз. Это особенно верно при использовании [VolatileState поставщика](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) как все состояние сохраняется в памяти.
- Из-за их [параллелизма на основе Включение](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) субъектов лучше всего использовать в качестве независимые объекты. Не создавайте графики нескольких субъектов, синхронных вызовов (каждый из которых скорее всего, становится отдельной сетевой вызов) или запросы циклическая субъекта; они будут существенно повлиять на производительность и масштабируемость.
- Не следует смешивать кода синхронизации с помощью асинхронного кода; ему являются асинхронными, так, чтобы предотвратить понижение производительности.
- Не выполнять длительные вызовы в субъектов, она заблокирует других вызовов одного субъекта, из-за Поочередный параллелизм.
- Если взаимодействует с другими службами с помощью [удаленное взаимодействие Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) и создании `ServiceProxyFactory`, затем создайте фабрику в [служба субъектов](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) уровень и *не*на уровне субъектов.


## <a name="application-diagnostics"></a>Диагностика приложения
- Тщательно добавление [ведение журнала приложения](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) в вызовов службы. Он помогает при диагностике из сценариев, где службы вызывать друг друга. Например, если A -> B -> C -> D, вызов может завершиться ошибкой в любом месте; Если не хватает ведения журнала, трудно диагностировать. Если службы вход слишком многое из-за количества обращений, по крайней мере не забудьте ведения журнала ошибок и предупреждений.

## <a name="iot-and-messaging-applications"></a>Интернет вещей и приложений обмена сообщениями
При чтении сообщений из [центра Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/) или [концентраторов событий](https://docs.microsoft.com/azure/event-hubs/) использовать [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) , интегрируется с Service Fabric Reliable Services для поддержки состояние чтения из концентратора событий, разделы и помещает новые сообщения к службам по `IEventProcessor::ProcessEventsAsync()` метод.


## <a name="design-guidance-on-azure"></a>Руководство по проектированию в Azure
* Посетите [Центр архитектуры Azure](https://docs.microsoft.com/azure/architecture/microservices/) инструкции по проектированию на [создания микрослужб в Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Посетите [начало работы с Azure для игр](https://docs.microsoft.com/gaming/azure/) инструкции по проектированию на [в службах игр с помощью Service Fabric](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
