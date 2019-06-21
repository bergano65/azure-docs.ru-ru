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
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203456"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric разработки рекомендации приложений

Эта статья содержит рекомендации по созданию приложений и служб в Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Ознакомиться с Service Fabric
* Чтение [, вы хотите узнать о Service Fabric?](service-fabric-content-roadmap.md) статьи.
* Узнайте о [сценарии приложений Service Fabric](service-fabric-application-scenarios.md).
* Понять модель вариантами в программировании, считывая [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Руководство по проектированию приложений
Ознакомиться с [общей архитектуре](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) приложений Service Fabric и их [рекомендации по проектированию](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Выберите шлюз API
Используйте службу шлюза API, который взаимодействует с серверными службами, которые можно легко масштабировать. Ниже приведены наиболее распространенные службы шлюза API, используемые.

- [Служба управления API Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), который является [интегрируется с Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Центр Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/) или [концентраторов событий](https://docs.microsoft.com/azure/event-hubs/), с использованием [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) для чтения из разделов в концентраторе событий.
- [Træfik обратный прокси-сервер](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), с использованием [поставщик Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Шлюз приложений Azure.](https://docs.microsoft.com/azure/application-gateway/)

   > [!NOTE] 
   > Шлюз приложений Azure не интегрирован с Service Fabric напрямую. Служба управления API Azure обычно является предпочтительным вариантом.
- Собственные пользовательские [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) шлюз веб-приложения.

### <a name="stateless-services"></a>Службы без отслеживания состояния
Мы рекомендуем всегда запускать при создании службы без отслеживания состояния с помощью [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) и сохранение состояния в базе данных Azure, Azure Cosmos DB или службы хранилища Azure. Реализованное состояние знакомую подход для большинства разработчиков. Этот подход также позволяет воспользоваться преимуществами возможности запросов в хранилище.  

### <a name="when-to-use-stateful-services"></a>Использование служб с отслеживанием состояния
Если сценарий для обеспечения минимальной задержки и должны хранить данные близко к вычислительных ресурсов, рассмотрите возможность служб с отслеживанием состояния. Примеры сценариев включают цифровой двойник устройства Интернета вещей, состояния игры, состояния сеанса, кэширование данных из базы данных и длительных рабочих процессов для отслеживания вызовов к другим службам.

Определите значения интервала времени хранения данных.

- **Кэшированные данные**. Используйте кэширование, когда задержка в внешнего хранилища. Использовать как свой собственный кэш данных службы с отслеживанием состояния или рассмотрите возможность использования [SoCreate Service Fabric распределенного кэша с открытым исходным](https://github.com/SoCreate/service-fabric-distributed-cache). В этом случае не нужно заниматься при потере всех данных в кэше.
- **Привязанный ко времени данных**. В этом случае вам нужно хранить данные рядом для вычисления в течение заданного времени для задержки, но вы можете позволить себе потерять данные в *аварийного*. К примеру во многих решениях Интернета вещей, информация должна быть close для вычислений, например когда Вычисление средней температуры за последние несколько дней, но при потере данным в определенных точках данных записаны не важных. Кроме того в этом сценарии вас не интересуют обычно резервное копирование отдельных точках данных. Только резервное копирование вычисляемых средних значений, которые периодически записываются во внешнее хранилище.  
- **Долгосрочного хранения данных**. Reliable collections можно хранить данные без возможности восстановления. Но в этом случае необходимо [Подготовка к аварийному восстановлению](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), в том числе [Настройка периодического резервного копирования политик](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) для кластеров. По сути настройкой, что происходит при удалении кластера в случае аварии, где необходимо создать новый кластер и способ развертывания новых экземпляров приложения и восстановить из последней резервной копии.

Сократить расходы и повысить уровень доступности:
- Можно сократить затраты с помощью служб с отслеживанием состояния, так как не взималась доступ к данным и стоимость операций из удаленного хранилища и так как не нужно использовать другую службу, например Azure кэша для Redis.
- С помощью служб с отслеживанием состояния, в первую очередь для хранилища, а не для вычислительных ресурсов и не рекомендуется. Считать служб с отслеживанием состояния вычислительных ресурсов с помощью недорогих локального хранилища.
- Путем удаления зависимости от других служб, можно повысить доступность службы. Управление состоянием с высоким уровнем ДОСТУПНОСТИ в кластере, можно изолировать от других время простоя службы или проблем с задержкой.

## <a name="how-to-work-with-reliable-services"></a>Работа с Reliable Services
Service Fabric Reliable Services позволяет легко создавать службы с отслеживанием и без отслеживания состояния. Дополнительные сведения см. в разделе [Обзор надежных служб](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Всегда учитывает [токен отмены](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) в `RunAsync()` метод для служб без отслеживания состояния и с отслеживанием состояния и `ChangeRole()` метод для служб с отслеживанием состояния. Если этого не сделать, Service Fabric не знает, если служба может быть закрыт. Например если не учитывает маркер отмены, может возникнуть гораздо более длительное время обновления приложения.
-   Открывающие и закрывающие [прослушивателей связи](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) вовремя и могут получать токены отмены.
-   Запрещается совместное использование кода синхронизации с помощью асинхронного кода. Например, не используйте `.GetAwaiter().GetResult()` в асинхронных вызовах. Использование async *вплоть* через стек вызовов.

## <a name="how-to-work-with-reliable-actors"></a>Работа с Reliable Actors
Service Fabric Reliable Actors позволяет легко создавать субъекты с отслеживанием состояния, виртуальный. Дополнительные сведения см. в разделе [Общие сведения о надежных субъектах](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Рассмотрите возможность с помощью pub/sub, обмен сообщениями между субъекты для масштабирования приложения. Инструменты, предоставляющие этой службы включить [Service Fabric открытым исходным кодом SoCreate Pub/Sub](https://service-fabric-pub-sub.socreate.it/) и [служебной шины Azure](https://docs.microsoft.com/azure/service-bus/).
- Состояния субъекта, как сделать [детализированные максимально](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Управление [субъекта жизненного цикла](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Удаление субъектов, если вы не собираетесь их использовать. Удаление ненужных субъектов особенно важна при использовании [Непостоянное состояние поставщика](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), так как все состояние сохраняется в памяти.
- Из-за их [Поочередный параллелизм](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), субъекты лучше всего использовать в качестве независимые объекты. Не создать графы вызовов нескольких субъектов, синхронный метод (каждый из которых скорее всего, становится отдельной сетевой вызов) или запросов циклическая субъекта. Они будут существенно повлиять на производительность и масштабируемость.
- Не следует смешивать кода синхронизации с помощью асинхронного кода. Используйте async последовательно во избежание проблем с производительностью.
- Не выполнять длительные вызовы в субъектов. Долго действующие вызовы заблокирует других вызовов одного субъекта, из-за Поочередный параллелизм.
- Если вы обмен данными с другими службами с использованием [удаленное взаимодействие Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) и вы создаете `ServiceProxyFactory`, создать фабрику в [субъекта службы](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) уровень и *не* на уровне субъектов.


## <a name="application-diagnostics"></a>Диагностика приложения
Тщательно о добавлении [ведение журнала приложения](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) в вызовов службы. Он поможет диагностировать сценариев, в которых службы вызывать друг друга. Например когда вызывает B вызывает C вызывает D, вызов может завершиться ошибкой в любом месте. Если у вас недостаточно ведения журнала, сбои, трудно диагностировать. Если службы вход слишком многое из-за количества обращений, убедитесь, что по крайней мере ведения журнала ошибок и предупреждений.

## <a name="iot-and-messaging-applications"></a>Интернет вещей и приложений обмена сообщениями
Когда вы читаете сообщения от [центра Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/) или [концентраторов событий](https://docs.microsoft.com/azure/event-hubs/), использовать [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor интегрируется с Service Fabric Reliable Services для поддержания состояния чтения из концентратора событий, разделы и помещает новые сообщения к службам по `IEventProcessor::ProcessEventsAsync()` метод.


## <a name="design-guidance-on-azure"></a>Руководство по проектированию в Azure
* Посетите [Центр архитектуры Azure](https://docs.microsoft.com/azure/architecture/microservices/) инструкции по проектированию на [создания микрослужб в Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Посетите [начало работы с Azure для игр](https://docs.microsoft.com/gaming/azure/) инструкции по проектированию на [с помощью Service Fabric в службах игр](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
