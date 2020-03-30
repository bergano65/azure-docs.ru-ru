---
title: Лучшие практики разработки приложений Azure Service Fabric
description: Рекомендации по разработке приложений и сервисов с использованием Сервиса Azure Fabric.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 876980bd6a59bace9ab4e490358964d19fa52c7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586093"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Лучшие практики разработки приложений Azure Service Fabric

В этой статье содержатся рекомендации по наилучшей практике для создания приложений и служб на Базе службы Azure.
 
## <a name="get-familiar-with-service-fabric"></a>Знакомство с сервисной тканью
* Читайте [Так вы хотите узнать о сервисной ткани?](service-fabric-content-roadmap.md)
* Читайте о [сценариях приложений Service Fabric](service-fabric-application-scenarios.md).
* Понять выбор модели программирования, прочитав [обзор модели программирования Service Fabric.](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Руководство по проектированию приложений
Ознакомьтесь с [общей архитектурой](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) приложений Service Fabric и их [соображениями проектирования.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>Выберите шлюз API
Используйте службу шлюза API, которая общается с службами бэк-энда, которые затем могут быть масштабированы. Наиболее распространенными службами шлюза API являются:

- [Управление API Azure,](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview) [интегрированное с Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Концентратор Azure IoT](https://docs.microsoft.com/azure/iot-hub/) или [концентраторы событий Azure](https://docs.microsoft.com/azure/event-hubs/)с помощью [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) для чтения из разделов Event Hub.
- [Tr'fik обратный прокси](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), с помощью [поставщика услуг Azure Service Fabric](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Шлюз приложений Azure.](https://docs.microsoft.com/azure/application-gateway/)

   > [!NOTE] 
   > Azure Application Gateway напрямую не интегрирован с Service Fabric. Управление API Azure, как правило, является предпочтительным выбором.
- Ваш собственный пользовательский [ASP.NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) Core веб-приложений шлюза.

### <a name="stateless-services"></a>Услуги без гражданства
Мы рекомендуем вам всегда начинать с создания служб без состояния, используя [надежные службы](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) и храня состояние в базе данных Azure, Azure Cosmos DB или Azure Storage. Внешнее состояние является более привычным подходом для большинства разработчиков. Этот подход также позволяет использовать возможности запроса в магазине.  

### <a name="when-to-use-stateful-services"></a>Когда использовать службы stateful
Рассмотрим службы состояния, когда у вас есть сценарий низкой задержки и необходимо держать данные близко к вычислению. Некоторые примеры сценариев включают ioT цифровых двухустройств, состояние игры, состояние сеанса, кэширование данных из базы данных и длительные рабочие процессы для отслеживания вызовов в другие службы.

Определите временные рамки хранения данных:

- **Кэшированные данные**. Используйте кэширование, когда задержка в внешних магазинах является проблемой. Используйте службу состояния в качестве собственного кэша данных или рассмотрите возможность использования [распределенного кэша SoCreate Service Fabric.](https://github.com/SoCreate/service-fabric-distributed-cache) В этом случае вам не нужно беспокоиться, если вы потеряете все данные в кэше.
- **Данные, связанные с графиком.** В этом случае необходимо держать данные близко к вычислению в течение определенного периода времени для задержки, но вы можете позволить себе потерять данные в *случае аварии.* Например, во многих решениях IoT данные должны быть близки к вычислениям, например, когда рассчитывается средняя температура за последние несколько дней, но если эти данные потеряны, определенные точки данных, записанные не так важны. Кроме того, в этом сценарии обычно не заботится о резервном копировании отдельных точек данных. Вы только резервное копирование вычисляемых средних значений, которые периодически записываются на внешнее хранилище.  
- **Долгосрочные данные**. Надежные коллекции могут хранить ваши данные постоянно. Но в этом случае необходимо [подготовиться к аварийному восстановлению,](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)включая [настройку периодических политик резервного копирования](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) для кластеров. По сути, вы настраиваете, что произойдет, если кластер будет уничтожен в результате аварии, где потребуется создать новый кластер, и как развернуть новые экземпляры приложений и восстановиться после последнего резервного копирования.

Экономия затрат и повышение доступности:
- Вы можете сократить затраты, используя службы stateful, потому что вы не несете затраты на доступ к данным и транзакции из удаленного хранилища, а также потому, что вам не нужно использовать другую службу, например Azure Cache для Redis.
- Использование государственных услуг в первую очередь для хранения, а не для вычислений стоит дорого, и мы не рекомендуем его. Думайте о государственных услугах, как о вычислении с дешевым локальным хранилищем.
- Удалив зависимости от других служб, можно улучшить доступность службы. Управление состоянием с помощью HA в кластере изолирует вас от других проблем простоев службы или проблем с задержкой.

## <a name="how-to-work-with-reliable-services"></a>Как работать с надежными службами
СервисНые услуги Fabric Reliable Services позволяют легко создавать услуги без гражданства и состояния. Для получения дополнительной информации смотрите [введение в надежные услуги](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Всегда чтите [токен отмены](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) в методе `RunAsync()` для `ChangeRole()` служб без состояния и состоянии состояния и методе предоставления услуг состояния. Если вы этого не сможете, СервисНая ткань не знает, можно ли закрыть вашу службу. Например, если вы не выполняете маркер отмены, может произойти гораздо больше времени обновления приложения.
-   Открытое и близкое [общение слушателей](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) своевременно, и честь отмены токенов.
-   Никогда не смешивайте код синхронизации с кодом async. Например, не используйте `.GetAwaiter().GetResult()` в своих вызовах async. Используйте async *весь путь* через стек вызова.

## <a name="how-to-work-with-reliable-actors"></a>Как работать с надежными актерами
Сервис Fabric Надежные актеры позволяют легко создавать штатные, виртуальные актеры. Для получения дополнительной информации, смотрите [введение в надежных актеров](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Серьезно рассмотреть вопрос об использовании паб / суб сообщений между субъектами для масштабирования приложения. Инструменты, которые предоставляют эту услугу включают [с открытым исходным кодом SoCreate службы Ткань паб / Sub](https://service-fabric-pub-sub.socreate.it/) и [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Сделайте состояние актера [как можно более гранулированным.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)
- Управление [жизненным циклом актера.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices) Удалите актеров, если вы не собираетесь использовать их снова. Удаление ненужных субъектов особенно важно, когда вы используете [нестабильное состояние поставщика,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)потому что все состояние хранится в памяти.
- Из-за их [пошаговой параллелизма,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)актеры лучше всего использовать в качестве независимых объектов. Не создавайте графики многоактных, синхронных вызовов метода (каждый из которых, скорее всего, становится отдельным сетевым вызовом) или не создавайте круговые запросы на актера. Это существенно повлияет на производительность и масштаб.
- Не смешивайте код синхронизации с кодом async. Используйте async последовательно для предотвращения проблем с производительностью.
- Не делайте длительных звонков в актеров. Долгосрочные вызовы будут блокировать другие вызовы тому же актеру из-за пошаговой параллелизма.
- Если вы общаетесь с другими службами, используя [сервисную перезагрузку Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) и `ServiceProxyFactory`создаете фабрику на уровне [обслуживания актеров,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) а *не* на уровне актера.


## <a name="application-diagnostics"></a>Диагностика приложения
Будьте тщательны о [добавлении регистрации приложений](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) в вызовы службы. Это поможет вам диагностировать сценарии, в которых службы звонят друг другу. Например, когда вызов ы B вызывает C, вызов может выполнить неудачу в любом месте. Если у вас недостаточно журналов, сбои трудно диагностировать. Если службы слишком много регистрируются из-за объемов вызовов, не забудьте хотя бы завести ошибки и предупреждения в журнале.

## <a name="iot-and-messaging-applications"></a>IoT и приложения для обмена сообщениями
При чтении сообщений из [концентратора Azure IoT](https://docs.microsoft.com/azure/iot-hub/) или [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)воспользуйтесь [ServiceFabricProcessor.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) ServiceFabricProcessor интегрируется с сервисными службами Fabric Reliable Services для поддержания состояния чтения с `IEventProcessor::ProcessEventsAsync()` разделов концентратора событий и переносит новые сообщения в ваши службы с помощью метода.


## <a name="design-guidance-on-azure"></a>Руководство по дизайну Azure
* Посетите [архитектурный центр Azure](https://docs.microsoft.com/azure/architecture/microservices/) для разработки рекомендаций по [созданию микрослужб на Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

* Посетите [Get Started с Azure для игр](https://docs.microsoft.com/gaming/azure/) для разработки руководства по [использованию сервисной ткани в игровых услугах.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
