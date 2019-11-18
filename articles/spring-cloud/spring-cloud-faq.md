---
title: Часто задаваемые вопросы о Azure Веснного облака | Документация Майкрософт
description: В этой статье содержатся ответы на часто задаваемые вопросы о Microsoft Azure Веснного облака.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 40275b3f643517236db26be8c2784144526fb7a1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151743"
---
# <a name="azure-spring-cloud-faq"></a>Вопросы и ответы по Azure весн Cloud

В этой статье содержатся ответы на часто задаваемые вопросы о Microsoft Azure Веснного облака. 

## <a name="general"></a>Общие

### <a name="why-azure-spring-cloud"></a>Вопросы и ответы по Azure Spring Cloud.

Azure Веснного облака предоставляет платформу как услугу (PaaS) для разработчиков весны облака. Azure Веснного облака управляет инфраструктурой приложений, чтобы вы могли сосредоточиться на коде приложения и бизнес-логике. К основным функциям, встроенным в Azure Веснного облака, относятся Еурека, сервер конфигурации, сервер реестра службы, сводная служба сборки, развертывание синего цвета и многое другое. Эта служба также позволяет разработчикам привязывать свои приложения к другим службам Azure, таким как Azure Cosmos DB, база данных Azure для MySQL и кэш Azure для Redis.

Azure Веснного облака расширяет возможности диагностики приложений для разработчиков и операторов, интегрируя Azure Monitor, Application Insights и Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Планы обслуживания, которые предлагает предложение Azure Spring Cloud

Azure Spring Cloud предлагает один план обслуживания на время действия предварительной версии.  Пружинное развертывание в облаке содержит 16 виртуальных ЦП ядер и 32 ГБ памяти.  Верхняя граница каждого экземпляра микрослужбы в развертывании — 4 ядра виртуальных ЦП с 8 ГБ памяти.

Resource (Ресурс) | Сумма
------- | -------
Экземпляры приложений в облачном приложении весны | 20
Всего экземпляров приложений на экземпляр службы Azure Spring Cloud | 50*
Экземпляры службы Azure Spring Cloud по регионам на одну подписку | 2*
Постоянные тома | 10 x 50 ГБ

\* _чтобы увеличить это ограничение, откройте запрос в [службу поддержки](https://azure.microsoft.com/support/faq/)._

Дополнительные сведения см. в статье [часто задаваемые вопросы о поддержке Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Насколько безопасным является облако Azure Spring Cloud?

Безопасность и конфиденциальность являются важнейшими приоритетами для клиентов Azure и весны в облаке Azure. Azure гарантирует, что только у клиентов есть доступ к данным приложения, журналам или конфигурациям, безопасно шифруя все эти данные. Все экземпляры службы в Azure Spring Cloud изолированы друг от друга.

Azure Spring Cloud предоставляет полное управление протоколом SSL и сертификатами.

Критические исправления безопасности для сред выполнения Azure Spring Cloud и OpenJDK применяются как можно скорее в Azure Spring Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>В каких регионах доступно облако Azure весны?

Восточная часть США, Западная часть США 2, Западная Европа и Юго-Восточная Азия.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Каковы известные ограничения Azure Веснного облака?

Во время действия предварительной версии облако Azure весны имеет следующие известные ограничения.

* `spring.application.name` будет переопределено именем приложения, которое используется для создания каждого приложения.
* `server.port` не разрешено в файле конфигурации из репозитория Git. Его добавление в файл конфигурации, скорее всего, сделает приложение недоступным из других приложений или Интернета.
* Шаблоны портал Azure и Azure Resource Manager не поддерживают отправку пакетов приложений. Пакеты приложений можно отправлять только путем развертывания приложения с помощью Azure CLI.
* Дополнительные сведения об ограничениях квоты см. в статье [какие планы обслуживания предлагает облачное предложение Azure](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Как можно отправить отзыв и сообщить о проблемах?

Если вы столкнулись с проблемами с облаком Azure весны, создайте [запрос в службу поддержки Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Чтобы отправить запрос на функцию или оставить отзыв, перейдите в службу [отзывов Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Разработка

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Я являюсь пружинным разработчиком облачных решений, но не впервые работаю в Azure. Каков самый быстрый способ научиться разрабатывать облачные приложения Azure для весны?

Чтобы быстро приступить к работе с Azure "пружинное облако", следуйте инструкциям в [кратком руководстве по запуску облачного приложения Azure весны с помощью портал Azure](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Какие среды выполнения Java поддерживает Azure Spring Cloud?

Azure Spring Cloud поддерживает Java 8 и 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Где можно просматривать журналы и метрики приложений весны облака?

Метрики находятся на вкладке "Обзор приложения" и [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

Azure Веснного облака поддерживает экспорт журналов облачных приложений и метрик в службу хранилища Azure, EventHub и [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Имя таблицы в Log Analytics — *аппплатформлогсфорспринг*. Сведения о том, как ее включить, см. в разделе [службы диагностики](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Поддерживает ли Azure Spring Cloud распределенную трассировку?

Да. Дополнительные сведения см. в разделе [учебник. Использование распределенной трассировки с помощью Azure веснного облака](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Какие типы ресурсов поддерживает привязка к службам?

Сейчас поддерживаются три службы: Azure Cosmos DB, база данных Azure для MySQL и кэш Azure для Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Можно ли просматривать, добавлять или перемещать постоянные тома в приложениях?

Да.

## <a name="deployment"></a>Развертывание

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Поддерживает ли Azure Веснное облако развертывание со синим зеленым цветом?
Да. Дополнительные сведения см. в разделе [Настройка промежуточной среды](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Можно ли получить доступ к Kubernetes для управления контейнерами приложений?

Нет  Azure Spring Cloud абстрагирует разработчика от базовой архитектуры, позволяя сосредоточиться на коде приложения и бизнес-логике.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Поддерживает ли Azure Spring Cloud создание контейнеров из источника?

Да. Дополнительные сведения см. [в статье запуск приложения "Весна Cloud" из исходного кода](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Поддерживает ли Azure Spring Cloud автоматическое масштабирование в экземплярах приложений?

Нет

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Каковы рекомендации по переносу существующих микрослужб с высокой задолженностью в облако Azure весны?

При переносе существующих микрослужб облачного облака в Azure Веснного облака рекомендуется следовать приведенным ниже рекомендациям.
* нужно разрешить все зависимости приложений.
* Подготовьте записи конфигурации, переменные среды и параметры ВИРТУАЛЬНОЙ машины Java, чтобы их можно было сравнить с развертыванием в Azure Веснного облака.
* Если вы хотите использовать привязку службы, перейдите к службам Azure и убедитесь, что установлены соответствующие права доступа.
* Рекомендуется удалить или отключить все внедренные службы, которые могут конфликтовать со службами, управляемыми Azure Веснным облаком, такими как служба обнаружения служб, сервер конфигурации и т. д.
* Рекомендуется использовать официальные и стабильные сводные библиотеки. Неофициальная, бета-версия или разветвленная версия низкоуровневых библиотек не поддерживает соглашение об уровне обслуживания (SLA).

После миграции Отслеживайте метрики ЦП и ОЗУ и сетевой трафик, чтобы убедиться, что экземпляры приложения масштабируются соответствующим образом.

## <a name="next-steps"></a>Дополнительная информация

Если у вас возникнут другие вопросы, см. [руководство по устранению неполадок в облаке Azure весны](spring-cloud-troubleshoot.md).
