---
title: Часто задаваемые вопросы об Azure Spring Cloud | Документация Майкрософт
description: Ознакомьтесь с вопросами и ответами по работе с Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 69b3827bbb53a5f989f3bdec0d5c4ea6035130f0
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592306"
---
# <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

В этой статье представлены ответы на часто задаваемые вопросы об Azure Spring Cloud. 

## <a name="general"></a>Общие сведения

### <a name="why-azure-spring-cloud"></a>Вопросы и ответы по Azure Spring Cloud.

Azure Spring Cloud предоставляет платформу как услугу (PaaS) для разработчиков Spring. Azure Spring Cloud управляет инфраструктурой приложений, что позволяет сосредоточиться на коде приложения и бизнес-логике. Основные функции, встроенные в Azure Spring Cloud, включают в себя Eureka, сервер конфигурации, сервер реестра службы, Pivotal Build Service, развертывание по методу Blue-Green и многое другое. Эта служба также позволяет разработчикам привязывать свои приложения к службам Azure, таким как CosmosDB, MySQL и кэш Azure для Redis.

Azure Spring Cloud расширяет возможности диагностики приложений для разработчиков и операторов, интегрируя Azure Monitor, Application Insights и Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Планы обслуживания, которые предлагает предложение Azure Spring Cloud

Azure Spring Cloud предлагает один план обслуживания на время действия предварительной версии.  Развертывание Spring Cloud содержит 16 ядер виртуального ЦП и 32 ГБ памяти.  Верхняя граница каждого экземпляра микрослужбы в развертывании составляет 4 ядра виртуального ЦП с 8 ГБ памяти.

Ресурс | Сумма
------- | -------
Экземпляры приложения на приложение Spring | 20
Всего экземпляров приложений на экземпляр службы Azure Spring Cloud | 50*
Экземпляры службы Azure Spring Cloud по регионам на одну подписку | 2*
Постоянные тома | 10 x 50 ГБ

*_Отправьте [запрос в службу поддержки](https://azure.microsoft.com/support/faq/), чтобы увеличить значения ограничений._

Дополнительные сведения см. на странице [Часто задаваемые вопросы о поддержке Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Насколько безопасным является облако Azure Spring Cloud?

Безопасность и конфиденциальность — это один из основных приоритетов для клиентов Azure и Azure Spring Cloud. Azure гарантирует, что только клиент имеет доступ к данным приложения, журналам или конфигурациям, безопасно шифруя все эти данные. Все экземпляры службы в Azure Spring Cloud изолированы друг от друга.

Azure Spring Cloud предоставляет полное управление протоколом SSL и сертификатами.

Критические исправления безопасности для сред выполнения Azure Spring Cloud и OpenJDK применяются как можно скорее в Azure Spring Cloud.

### <a name="which-regions-azure-spring-cloud-are-available"></a>В каких регионах доступно Azure Spring Cloud?

Восточная часть США, Западная часть США 2, Западная Европа и Юго-Восточная Азия.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Каковы известные ограничения Azure Spring Cloud?

Ниже приведены известные ограничения для Azure Spring Cloud, пока служба предоставляется в предварительной версии.

* `spring.application.name` будет переопределено именем приложения, используемым для создания каждого приложения.
* `server.port` не допускается в файле конфигурации из репозитория Git. Добавление этого элемента в файл конфигурации, скорее всего, приведет к тому, что приложение будет недоступно из других приложений или из Интернета.
* Портал Azure и шаблоны диспетчера ресурсов не поддерживают отправку пакетов приложений. Это можно сделать только развернув приложение с помощью Azure CLI.
* Сведения об ограничениях квоты см. в разделе [Планы обслуживания, которые предоставляет предложение Azure Spring Cloud](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Как можно отправить отзыв и сообщить о проблемах?

Если вы создали экземпляры служб Spring, вы можете создать запрос в [службу поддержки Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Если вы еще не перешли в облако Azure Spring Cloud, перейдите на страницу [отзывов и предложений по Azure](https://feedback.azure.com/), чтобы запросить функции или оставить отзыв.

## <a name="development"></a>Разработка

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Я являюсь разработчиком Spring, но не знаком с Azure. Как быстрее всего научиться разрабатывать приложения в Azure Spring Cloud?

Быстрее всего приступить к работе с облаком Azure Spring Cloud можно, выполнив действия, описанные в [этом кратком руководстве](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Какие среды выполнения Java поддерживает Azure Spring Cloud?

Azure Spring Cloud поддерживает Java 8 и 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Где можно просмотреть журналы и метрики приложений Spring?

Метрики находятся на вкладке "Обзор приложения" и [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

Azure Spring Cloud поддерживает экспорт журналов и метрик приложений Spring в службу хранилища Azure, EventHub и [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Имя таблицы в Log Analytics — `AppPlatformLogsforSpring`. Чтобы включить эту возможность, ознакомьтесь с этой статьей о [службах диагностики](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Поддерживает ли Azure Spring Cloud распределенную трассировку?

Да, дополнительные сведения см. в статье о [распределенной трассировке](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Какие типы ресурсов поддерживает привязка к службам?

В настоящее время поддерживаются три службы: Azure Cosmos DB, База данных Azure для MySQL и Кэш Azure для Redis.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>Можно ли просматривать, добавлять и перемещать постоянные тома внутри приложений?
Да.

## <a name="deployment"></a>Развертывание

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Поддерживает ли Azure Spring Cloud развертывание по схеме Blue-Green?
Да. Дополнительные сведения см. в [руководстве по промежуточной среде](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Можно ли получить доступ к Kubernetes для управления контейнерами приложений?

№  Azure Spring Cloud абстрагирует разработчика от базовой архитектуры, позволяя сосредоточиться на коде приложения и бизнес-логике.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Поддерживает ли Azure Spring Cloud создание контейнеров из источника?

Да, дополнительные сведения см. на странице [Launch your Spring Cloud application from source code](spring-cloud-launch-from-source.md) (Запуск приложения Spring Cloud из исходного кода).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Поддерживает ли Azure Spring Cloud автоматическое масштабирование в экземплярах приложений?

№

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Каковы рекомендации по переносу имеющихся микрослужб Spring в Azure Spring Cloud?

Прежде чем переносить имеющиеся микрослужбы Spring в Azure Spring Cloud,
* нужно разрешить все зависимости приложений.
* Подготовьте записи конфигурации, переменные среды и параметры виртуальных машин Java, чтобы их можно было сравнить с развертыванием в Azure Spring Cloud.
* Если вы хотите использовать привязку службы, перейдите к службам Azure и убедитесь, что установлены соответствующие разрешения на доступ.
* Советуем удалить или отключить встроенные службы, которые могут конфликтовать со службами, управляемыми Azure Spring Cloud, такими как служба обнаружения служб, сервер конфигурации и т. д.
*-* Рекомендуем использовать официальные и стабильные библиотеки Pivotal Spring. Неофициальная версия, бета-версия или разветвленная версия библиотек Pivotal Spring не поддерживается в рамках Соглашения об уровне обслуживания.

После миграции отслеживайте метрики ЦП и ОЗУ и сетевой трафик, чтобы обеспечить правильное масштабирование экземпляров приложения.

## <a name="next-steps"></a>Дополнительная информация

[Руководство по устранению распространенных неполадок](spring-cloud-troubleshoot.md)