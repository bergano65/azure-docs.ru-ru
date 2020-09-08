---
title: Общие сведения об Azure Spring Cloud
description: Узнайте о преимуществах и возможностях развертывания приложений Java Spring и управления ими в Azure с помощью Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: aa3f1032301224701f5bfc08807e89194f263da6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255245"
---
# <a name="what-is-azure-spring-cloud"></a>Что представляет собой Azure Spring Cloud?

Azure Spring Cloud позволяет легко развертывать в Azure приложения для микрослужб на основе Spring Boot без внесения изменений в код.  Azure Spring Cloud управляет инфраструктурой приложений Spring Cloud, благодаря чему разработчики могут сосредоточиться на коде.  Spring Cloud обеспечивает управление жизненным циклом за счет комплексного мониторинга и диагностики, управления конфигурацией, обнаружения служб, интеграции CI/CD, выполнения развертываний по схеме Blue-Green и прочего.

Azure Spring Cloud, как часть экосистемы Azure, позволяет легко добавлять привязки к другим службам Azure, включая хранилище, базы данных, службы мониторинга и многое другое.

В этой вводной статье описан сервер конфигурации Azure Spring Cloud и показано, как включить развертывания Blue/Green, масштабировать приложения и отслеживать производительность приложения.

## <a name="spring-cloud-config-server"></a>Сервер конфигурации Spring Cloud

Сервер конфигурации Azure Spring Cloud предоставляет возможности внешней конфигурации в распределенной системе с поддержкой на стороне сервера и клиента.  Azure Spring Cloud — это центральное расположение для управления свойствами приложения во всех средах. См. сведения о [конфигурации Spring Cloud](https://spring.io/projects/spring-cloud-config.md). 

## <a name="enable-bluegreen-deployments"></a>Включение развертываний по схеме Blue-Green

В Azure Spring Cloud поддерживаются развертывания по схеме Blue-Green, позволяющие выпускать решения и обновлять код в рабочих средах.  Эта схема управления изменениями позволяет разработчикам реализовывать функции и вносить изменения кода с возможность немедленного возврата к исходному состоянию.  Разработчики могут сосредоточиться на написании кода с использованием нескольких рабочих сред и для обновления кода или отката изменений без прерывания работы приложения.  Дополнительные сведения о промежуточных средах и развертываниях по схеме Blue-Green см. в [этой статье](spring-cloud-howto-staging-environment.md).

## <a name="automate-cicd-pipelines"></a>Автоматизация конвейеров CI/CD

Azure Spring Cloud обеспечивает интеграцию с Azure DevOps с помощью Azure CLI.  Решения Azure DevOps позволяют автоматизировать интеграцию кода и развертывание в приложение Spring.  Дополнительные сведения см. в [этой статье](spring-cloud-howto-cicd.md).

## <a name="scale-your-application"></a>Масштабирование приложения

С помощью Azure Spring Cloud вы можете легко масштабировать микрослужбы на панели мониторинга Azure Spring Cloud.  Количество виртуальных ЦП и объем памяти, доступной для микрослужб, можно масштабировать в соответствии со своими потребностями.  Масштабирование вступает в силу мгновенно и не требует изменения кода или повторного развертывания.  Дополнительные сведения см. в [этом учебнике](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Мониторинг приложений

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Мониторинг приложения с помощью распределенной трассировки и Azure App Insights

Средства распределенной трассировки Spring Cloud позволяют разработчикам выполнять отладку и мониторинг сложных взаимодействий между микрослужбами в приложении.  Интегрировав [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) с [Azure Application Insights](../azure-monitor/insights/insights-overview.md) вы получите мощную возможность распределенной трассировки с портала Azure.  Дополнительные сведения см. в [этом учебнике](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Дальнейшие действия
Чтобы начать работу, выполните инструкции из краткого руководства по Spring Cloud:
> [!div class="nextstepaction"]
> [Краткое руководство. Развертывание первого приложения Azure Spring Cloud](spring-cloud-quickstart.md)

Дополнительные примеры доступны на GitHub: [Примеры для Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
