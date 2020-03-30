---
title: Часто задаваемые вопросы о весеннем облаке Azure Документы Майкрософт
description: В этой статье часто задаются вопросы о облачности Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298760"
---
# <a name="azure-spring-cloud-faq"></a>Часто задаваемые вопросы в облачных задаваемых вопросов Azure

В этой статье часто задаются вопросы о облачности Azure Spring Cloud. 

## <a name="general"></a>Общие сведения

### <a name="why-azure-spring-cloud"></a>Вопросы и ответы по Azure Spring Cloud.

Облако Azure Spring Cloud предоставляет платформу в качестве службы (PaaS) для разработчиков Spring Cloud. Облако Azure Spring Cloud управляет инфраструктурой приложений, чтобы можно было сосредоточиться на коде приложения и бизнес-логике. Основные функции, встроенные в облако Azure Spring, включают Eureka, Config Server, сервер реестра служб, службу pivotal Build Service, сине-зеленые развертывания и многое другое. Эта служба также позволяет разработчикам связывать свои приложения с другими службами Azure, такими как Azure Cosmos DB, база данных Azure для MyS'L и Azure Cache для Redis.

Облако Azure Spring Cloud улучшает работу по диагностике приложений для разработчиков и операторов, интегрируя Azure Monitor, Application Insight sandands и Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Планы обслуживания, которые предлагает предложение Azure Spring Cloud

Azure Spring Cloud предлагает один план обслуживания на время действия предварительной версии.  Развертывание Spring Cloud содержит 16 ядер vCPU и 32 гигабайта (ГБ) памяти.  Верхняя граница для каждого экземпляра микрослужбы в рамках развертывания — 4 ядра vCPU с 8 ГБ памяти.

Ресурс | Сумма
------- | -------
Экземпляры приложения на приложение Spring | 20
Всего экземпляров приложений на экземпляр службы Azure Spring Cloud | 500
Экземпляры службы Azure Spring Cloud по регионам на одну подписку | 10
Постоянные тома | 10 x 50 ГБ

\*_Чтобы повысить лимит, откройте [опорный билет.](https://azure.microsoft.com/support/faq/)_

Для получения дополнительной информации смотрите [часто задаваемые вопросы службы поддержки Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Насколько безопасным является облако Azure Spring Cloud?

Безопасность и конфиденциальность являются одними из главных приоритетов для клиентов Azure и Azure Spring Cloud. Azure помогает гарантировать, что только клиенты имеют доступ к данным приложения, журналам или конфигурациям, надежно шифруя все эти данные. Все экземпляры службы в Azure Spring Cloud изолированы друг от друга.

Облако Azure Spring Cloud обеспечивает полное управление TLS/SSL и сертификатами.

Критические исправления безопасности для сред выполнения Azure Spring Cloud и OpenJDK применяются как можно скорее в Azure Spring Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>В каких регионах доступно облако Azure Spring Cloud?

Восточная часть США, Западная часть США 2, Западная Европа и Юго-Восточная Азия.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Каковы известные ограничения весеннего облака Azure?

Во время предварительного выпуска облако Azure Spring Cloud имеет следующие известные ограничения:

* `spring.application.name`будет переопределено именем приложения, которое используется для создания каждого приложения.
* `server.port`не допускается в файле конфигурации из репо Git. Добавление его в файл конфигурации, скорее всего, сделает ваше приложение недоступным из других приложений или Интернета.
* Портал Azure и шаблоны менеджера ресурсов Azure не поддерживают загрузку пакетов приложений. Загрузить пакеты приложений можно только развернув приложение через Azure CLI.
* Чтобы узнать об ограничениях квот, см. [Какие планы обслуживания предлагает Azure Spring Cloud?.](#what-service-plans-does-azure-spring-cloud-offer)

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Как можно отправить отзыв и сообщить о проблемах?

При возникновении проблем с облачным унизам Azure Spring — создайте [запрос на поддержку Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Чтобы отправить запрос на функцию или предоставить обратную связь, перейдите на [отзывы Azure.](https://feedback.azure.com/forums/34192--general-feedback)

## <a name="development"></a>Разработка

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Я разработчик весеннего облака, но новичок в Azure. Как мне быстрее узнать, как разрабатывать облачное приложение Azure Spring?

Чтобы получить самый быстрый способ начать работу с облачным узлом Azure Spring, следуйте инструкциям в [приложении «Быстрый запуск: Запустите облачное приложение Azure Spring» с помощью портала Azure.](spring-cloud-quickstart-launch-app-portal.md)

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Какие среды выполнения Java поддерживает Azure Spring Cloud?

Azure Spring Cloud поддерживает Java 8 и 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Где я могу просмотреть журналы и метрики приложений Spring Cloud?

Метрики находятся на вкладке "Обзор приложения" и [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

Облако Azure Spring поддерживает экспорт журналов и метрик в Spring Cloud в систему хранения и [метрики](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)Azure Storage, EventHub и Log Analytics. Название таблицы в журнале Analytics *AppPlatformLogsforSpring*. Чтобы узнать, как включить его, смотрите [Диагностические услуги](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Поддерживает ли Azure Spring Cloud распределенную трассировку?

Да. Для получения дополнительной информации [см.](spring-cloud-tutorial-distributed-tracing.md)

### <a name="what-resource-types-does-service-binding-support"></a>Какие типы ресурсов поддерживает привязка к службам?

В настоящее время поддерживаются три службы: Azure Cosmos DB, база данных Azure для MyS'L и Кэш Azure для Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Могу ли я просматривать, добавлять или перемещать постоянные тома из моих приложений?

Да.

## <a name="deployment"></a>Развертывание

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Поддерживает ли облако Azure Spring развертывание сине-зеленого цвета?
Да. Для получения дополнительной информации [см.](spring-cloud-howto-staging-environment.md)

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Можно ли получить доступ к Kubernetes для управления контейнерами приложений?

Нет.  Azure Spring Cloud абстрагирует разработчика от базовой архитектуры, позволяя сосредоточиться на коде приложения и бизнес-логике.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Поддерживает ли Azure Spring Cloud создание контейнеров из источника?

Да. Для получения дополнительной информации смотрите [приложение Launch Spring Cloud из исходного кода.](spring-cloud-launch-from-source.md)

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Поддерживает ли Azure Spring Cloud автоматическое масштабирование в экземплярах приложений?

Нет.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Каковы наилучшие методы перехода существующих микрослужб Spring Cloud в облако Azure Spring?

При переносе существующих микрослужб Spring Cloud в облако Azure Spring рекомендуется соблюдать следующие рекомендации:
* нужно разрешить все зависимости приложений.
* Подготовьте записи конфигурации, переменные среды и параметры JVM, чтобы можно было сравнить их с развертыванием в облачности Azure Spring.
* Если вы хотите использовать Привязку к службам, пройдите через службы Azure и убедитесь, что вы установили соответствующие разрешения доступа.
* Мы рекомендуем удалить или отключить все встроенные службы, которые могут противоречить службам, управляемым Облачным уэндасом Azure Spring, таким как наш сервис Service Discovery, Config Server и так далее.
* Мы рекомендуем использовать официальные, стабильные библиотеки Pivotal Spring. Неофициальные, бета-версии или раздвилосченные версии библиотек Pivotal Spring не имеют поддержки на уровне обслуживания (SLA).

После миграции следите за метриками процессора/RAM и сетевым трафиком, чтобы убедиться, что экземпляры приложения масштабируются надлежащим образом.

## <a name="next-steps"></a>Дальнейшие действия

Если у вас есть дополнительные вопросы, смотрите [руководство по устранению неполадок Azure Spring Cloud.](spring-cloud-troubleshoot.md)
