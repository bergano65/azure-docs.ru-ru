---
title: Рекомендации по проектированию приложений Service Fabric Azure | Документация Майкрософт
description: Рекомендации по разработке Service Fabric приложений.
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
ms.author: mfussell
ms.openlocfilehash: eec5daf0100d527886a508f5adbdb2b0e3010b09
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262266"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Рекомендации по проектированию приложений Service Fabric Azure

В этой статье приводятся рекомендации по созданию приложений и служб в Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Знакомство с Service Fabric
* Ознакомьтесь с тем, [что вы хотите узнать о Service Fabric?](service-fabric-content-roadmap.md)
* Ознакомьтесь с [Service Fabric сценариями приложений](service-fabric-application-scenarios.md).
* Сведения о выборе модели программирования см. в статье [Service Fabric модель программирования](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Руководство по проектированию приложений
Ознакомьтесь с [общей архитектурой](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) Service Fabric приложений и [соображениями проектирования](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Выбор шлюза API
Используйте службу шлюза API, которая взаимодействует со службами серверной части, которые затем можно масштабировать. Ниже перечислены наиболее часто используемые службы шлюза API.

- [Управление API Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), интегрированное [с Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Центр Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/) или [концентраторы событий Azure](https://docs.microsoft.com/azure/event-hubs/), использующие [Сервицефабрикпроцессор](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) для чтения из разделов концентратора событий.
- [Трæфик обратный прокси-сервер](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)с помощью [поставщика Service Fabric Azure](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Шлюз приложений Azure.](https://docs.microsoft.com/azure/application-gateway/)

   > [!NOTE] 
   > Шлюз приложений Azure не интегрируется напрямую с Service Fabric. Управление API Azure обычно является предпочтительным вариантом.
- Собственный пользовательский шлюз веб-приложений [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) .

### <a name="stateless-services"></a>Службы без отслеживания состояния
Рекомендуется всегда начинать с создания служб без отслеживания состояния, используя [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) и сохраняя состояние в базе данных azure, Azure Cosmos DB или службе хранилища Azure. Внешнее состояние — это более привычный подход для большинства разработчиков. Такой подход также позволяет использовать возможности запросов в магазине.  

### <a name="when-to-use-stateful-services"></a>Когда следует использовать службы с отслеживанием состояния
Рекомендуется использовать службы с отслеживанием состояния, если имеется сценарий с низкой задержкой и необходимо, чтобы данные были близки к вычислению. В число примеров сценариев входят устройства Интернета вещей Digital двойника, состояние игры, состояние сеанса, кэширование данных из базы данных и длительные рабочие процессы для отслеживания вызовов других служб.

Определите срок хранения данных:

- **Кэшированные данные**. При возникновении проблем с задержкой во внешних хранилищах используйте кэширование. Используйте службу с отслеживанием состояния в качестве собственного кэша данных или воспользуйтесь [Service Fabric сокреатеом распределенного кэша с открытым кодом](https://github.com/SoCreate/service-fabric-distributed-cache). В этом случае вам не нужно беспокоиться о потере всех данных в кэше.
- **Данные, привязанные к времени**. В этом случае необходимо, чтобы данные закрылись для вычислений в течение определенного времени задержки, но можно позволить себе потерять данные в случае *аварии*. Например, во многих решениях IoT данные должны быть близко к вычислению, например при вычислении средней температуры за последние несколько дней, но если эти данные потеряны, то зафиксированные точки данных не важны. Кроме того, в этом сценарии обычно не требуется создавать резервные копии отдельных точек данных. Вы только выполняете резервное копирование вычисленных средних значений, которые периодически записываются во внешнее хранилище.  
- **Долгосрочные данные**. Надежные коллекции могут хранить данные постоянно. Но в этом случае необходимо подготовиться [к аварийному восстановлению](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), включая [настройку периодических политик архивации](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) для кластеров. По сути, вы настраиваете то, что произойдет, если кластер уничтожается в случае аварии, где потребуется создать новый кластер, а также как развернуть новые экземпляры приложения и выполнить восстановление из последней резервной копии.

Экономьте расходы и повышайте доступность:
- Вы можете сократить затраты, используя службы с отслеживанием состояния, так как вы не будете получать расходы на доступ к данным и транзакции из удаленного хранилища, а также не хотите использовать другую службу, например кэш Azure для Redis.
- Использование служб с отслеживанием состояния, в основном для хранения, а не для вычислений, является дорогостоящим и не рекомендуется. Выдумайте о службах с отслеживанием состояния в виде вычислений с недорогым локальным хранилищем.
- Путем удаления зависимостей от других служб можно повысить доступность службы. Управление состоянием с высоким уровнем доступности в кластере изолирует вас от других простоев служб или проблем задержки.

## <a name="how-to-work-with-reliable-services"></a>Работа с Reliable Services
Service Fabric Reliable Services позволяет легко создавать службы без отслеживания состояния и с отслеживанием состояния. Дополнительные сведения см. в статье [Введение в Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Всегда соблюдайте [токен отмены](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) в `RunAsync()` методе для служб без отслеживания состояния и с отслеживанием состояния, а также `ChangeRole()` метод для служб с отслеживанием состояния. В противном случае Service Fabric не знает, можно ли закрыть службу. Например, если вы не принимаете маркер отмены, может произойти значительно больше времени обновления приложения.
-   Открывайте и закрывайте [прослушиватели связи](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) своевременно и соблюдайте токены отмены.
-   Никогда не смешивать код синхронизации с асинхронным кодом. Например, не используйте `.GetAwaiter().GetResult()` в асинхронных вызовах. Используйте в стеке *вызовов Async.*

## <a name="how-to-work-with-reliable-actors"></a>Работа с Reliable Actors
Service Fabric Reliable Actors позволяет легко создавать виртуальные субъекты с отслеживанием состояния. Дополнительные сведения см. в статье [Введение в Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Серьезно рассмотрите возможность использования обмена сообщениями между субъектами для масштабирования приложения. Средства, предоставляющие эту службу, включают в себя [сокреате с открытым исходным кодом Service Fabric Pub/](https://service-fabric-pub-sub.socreate.it/) подпрограммы и [служебная шина Azure](https://docs.microsoft.com/azure/service-bus/).
- Сделайте так, чтобы состояние субъекта было как [можно](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)более детализированным.
- Управление [жизненным циклом субъекта](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Удалите субъекты, если они не будут использоваться повторно. Удаление ненужных субъектов особенно важно, если вы используете [Временный поставщик состояний](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), так как все состояния хранятся в памяти.
- Из-за [параллелизма, основанной на](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)поочередности, субъекты лучше использовать как независимые объекты. Не создавайте графы синхронных вызовов методов с несколькими субъектами (каждый из которых, скорее всего, станет отдельным сетевым вызовом) или создайте запросы с циклическими субъектами. Это значительно повлияет на производительность и масштабируемость.
- Не смешивать код синхронизации с асинхронным кодом. Используйте асинхронный режим для предотвращения проблем с производительностью.
- Не делайте долго выполняющихся вызовов в субъектах. Длительные вызовы будут блокировать другие вызовы одного и того же субъекта из-за параллелизма, основанной на последующей блокировке.
- Если вы обмениваетесь данными с другими службами с помощью [Service Fabric удаленного взаимодействия](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) и `ServiceProxyFactory`создаете, Создайте фабрику на уровне [службы субъекта](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) , а *не* на уровне субъекта.


## <a name="application-diagnostics"></a>Диагностика приложений
Будьте в полной мере добавим [ведение журнала приложений](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) в вызовах служб. Она поможет в диагностике сценариев, в которых службы вызывают друг друга. Например, если вызов метода B вызывает C, то вызов может завершиться ошибкой в любом месте. Если ведение журнала не имеет достаточного количества, трудно диагностировать сбои. Если службы имеют слишком много записей из-за вызова томов, убедитесь, что по крайней мере ошибки в журнале и предупреждения.

## <a name="iot-and-messaging-applications"></a>Приложения IoT и Messaging
При чтении сообщений из [центра Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/) или [концентраторов событий Azure](https://docs.microsoft.com/azure/event-hubs/)используйте [сервицефабрикпроцессор](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). Сервицефабрикпроцессор интегрируется с Service Fabric Reliable Services для поддержания состояния чтения из разделов концентратора событий и отправки новых сообщений службам с помощью `IEventProcessor::ProcessEventsAsync()` метода.


## <a name="design-guidance-on-azure"></a>Руководство по проектированию в Azure
* Руководство по проектированию [микрослужб в Azure](https://docs.microsoft.com/azure/architecture/microservices/)см. в [центре архитектуры Azure](https://docs.microsoft.com/azure/architecture/microservices/) .

* Руководство по проектированию [с использованием Service Fabric в игровых службах](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)см. в статье [Начало работы с Azure для игр](https://docs.microsoft.com/gaming/azure/) .
