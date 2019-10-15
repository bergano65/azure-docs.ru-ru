---
title: Общие сведения об Azure Spring Cloud
description: Узнайте о преимуществах и возможностях развертывания приложений Java Spring и управления ими в Azure с помощью Azure Spring Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 10/04/2019
ms.author: jeconnoc
ms.openlocfilehash: c58e6b2a0c0de8295df65b44fbdeaeb2d4461948
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166532"
---
# <a name="what-is-azure-spring-cloud"></a>Сведения об Azure Spring Cloud

Azure Spring Cloud позволяет легко развертывать в Azure приложения для микрослужб на основе Spring Boot без внесения изменений в код.  Благодаря Azure Spring Cloud разработчики могут сосредоточиться на коде, управляя жизненным циклом приложений Spring Cloud.  Spring Cloud обеспечивает управление жизненным циклом за счет комплексного мониторинга и диагностики, управления конфигурацией, обнаружения служб, интеграции CI/CD, выполнения развертываний по схеме Blue-Green и прочего.

Azure Spring Cloud, как часть экосистемы Azure, позволяет легко добавлять привязки к другим службам Azure, включая хранилище, базы данных, службы мониторинга и многое другое.

В настоящее время Azure Spring Cloud находиться на этапе предварительной версии.  Чтобы получить доступ к предварительной версии этой службе, [заполните эту форму](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

Чтобы приступить к работе, ознакомьтесь с кратким руководство по запуску приложений Spring Cloud с помощью [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), [портала Azure](spring-cloud-quickstart-launch-app-portal.md) или [Maven](spring-cloud-quickstart-launch-app-maven.md).

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
