---
title: Общие сведения об Azure Spring Cloud
description: Узнайте о преимуществах и возможностях развертывания приложений Java Spring и управления ими в Azure с помощью Azure Spring Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: 87bcea5e9ada4f095896c1ebe4cbe1928f158eb0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895416"
---
# <a name="what-is-azure-spring-cloud"></a>Сведения об Azure Spring Cloud

Azure Spring Cloud позволяет легко развертывать в Azure приложения для микрослужб на основе Spring Boot без внесения изменений в код.  Azure Spring Cloud обеспечивает управление жизненным циклом приложений Spring Cloud, благодаря чему разработчики могут сосредоточиться на коде.  Spring Cloud обеспечивает управление жизненным циклом за счет комплексного мониторинга и диагностики, управления конфигурацией, обнаружения служб, интеграции CI/CD, выполнения развертываний по схеме Blue-Green и прочего.

Azure Spring Cloud, как часть экосистемы Azure, позволяет легко добавлять привязки к другим службам Azure, включая хранилище, базы данных, службы мониторинга и многое другое.

Azure Spring Cloud в настоящее время предлагается в качестве общедоступной предварительной версии. Предложения общедоступной предварительной версии позволяют клиентам поэкспериментировать с новыми функциями до официального выпуска.  Функции и службы общедоступной предварительной версии не предназначены для использования в рабочей среде.  Чтобы получить дополнительные сведения о поддержке на этапе использования предварительных версий, ознакомьтесь с разделом [Вопросы и ответы](https://azure.microsoft.com/support/faq/) или оформите [запрос на поддержку](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

Чтобы приступить к работе, ознакомьтесь с кратким руководство по запуску приложений Spring Cloud с помощью [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), [портала Azure](spring-cloud-quickstart-launch-app-portal.md) или [Maven](spring-cloud-quickstart-launch-app-maven.md).

Дополнительные примеры доступны на GitHub: [Примеры для Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Конфигурация приложения

### <a name="spring-cloud-config-server"></a>Сервер конфигурации Spring Cloud

Сервер конфигурации Azure Spring Cloud предоставляет возможности внешней конфигурации в распределенной системе с поддержкой на стороне сервера и клиента.  Это центральное расположение для управления свойствами приложения во всех средах.  Дополнительные сведения см. в [справочнике по сервере конфигурации Spring Cloud](https://spring.io/projects/spring-cloud-config.md) и в оставшейся части этого учебника.

### <a name="enable-bluegreen-deployments"></a>Включение развертываний по схеме Blue-Green

В Azure Spring Cloud поддерживаются развертывания по схеме Blue-Green, позволяющие выпускать решения и обновлять код в рабочих средах.  Эта схема управления изменениями позволяет разработчикам реализовывать функции и изменения кода с возможность немедленного возврата к исходному состоянию.  Благодаря Azure разработчики могут сосредоточиться на написании кода за счет управления несколькими рабочими средами и упрощения обновления кода или отката изменений без прерывания работы приложения.  Дополнительные сведения о промежуточных средах и развертываниях по схеме Blue-Green см. в [этой статье](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Автоматизация конвейеров CI/CD

Azure Spring Cloud обеспечивает интеграцию с Azure DevOps с помощью Azure CLI.  Решения Azure DevOps позволяют автоматизировать интеграцию кода и развертывание в приложение Spring.  Дополнительные сведения см. в [этой статье](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Масштабирование приложения

С помощью Azure Spring Cloud вы можете легко масштабировать микрослужбы на панели мониторинга Azure Spring Cloud.  Количество виртуальных ЦП и объем памяти, доступной для микрослужб, можно масштабировать в соответствии со своими потребностями.  Масштабирование вступает в силу мгновенно и не требует изменения кода или повторного развертывания.  Дополнительные сведения см. в [этом учебнике](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Мониторинг приложений

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Мониторинг приложения с помощью распределенной трассировки и Azure App Insights

Средства распределенной трассировки Spring Cloud позволяют разработчикам выполнять отладку и мониторинг сложных взаимодействий между микрослужбами в приложении.  Интегрировав [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) с [Azure Application Insights](../azure-monitor/insights/insights-overview.md) вы получите мощную возможность распределенной трассировки с портала Azure.  Дополнительные сведения см. в [этом учебнике](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Дополнительная информация

- [Краткое руководство. Запуск приложения Java Spring с помощью Azure CLI](spring-cloud-quickstart-launch-app-cli.md)
