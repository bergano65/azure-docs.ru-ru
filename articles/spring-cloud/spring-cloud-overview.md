---
title: Общие сведения об Azure Spring Cloud
description: Узнайте о преимуществах и возможностях развертывания приложений Java Spring и управления ими в Azure с помощью Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 74ebdafb835aff75f282b9d6ac02d8ccf672a2be
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501096"
---
# <a name="what-is-azure-spring-cloud"></a>Что представляет собой Azure Spring Cloud?

Azure Spring Cloud позволяет легко развертывать в Azure приложения для микрослужб на основе Spring Boot без внесения изменений в код.  Azure Spring Cloud управляет инфраструктурой приложений Spring Cloud, благодаря чему разработчики могут сосредоточиться на коде.  Spring Cloud обеспечивает управление жизненным циклом за счет комплексного мониторинга и диагностики, управления конфигурацией, обнаружения служб, интеграции CI/CD, выполнения развертываний по схеме Blue-Green и прочего.

Azure Spring Cloud поддерживает приложения Java [Spring Boot](https://spring.io/projects/spring-boot) и ASP.NET Core [Steeltoe](https://steeltoe.io/). Поддержка Steeltoe в настоящее время предлагается в общедоступной предварительной версии. Предложения в общедоступной предварительной версии позволяют экспериментировать с новыми функциями до официального выпуска.  Общедоступные предварительные версии функций и служб не предназначены для использования в рабочей среде.  Дополнительные сведения см. на странице [Часто задаваемые вопросы о поддержке Azure](https://azure.microsoft.com/support/faq/) или отправьте [запрос на поддержку](../azure-portal/supportability/how-to-create-azure-support-request.md).

Azure Spring Cloud, как часть экосистемы Azure, позволяет легко добавлять привязки к другим службам Azure, включая хранилище, базы данных, службы мониторинга и многое другое.

В этой вводной статье описаны следующие возможности Azure Spring Cloud.

* Сервер конфигурации
* Развертывания по схеме Blue-Green
* Масштабирование приложения
* Интеграция с Azure DevOps
* Мониторинг приложений

## <a name="spring-cloud-config-server"></a>Сервер конфигурации Spring Cloud

Сервер конфигурации Azure Spring Cloud предоставляет возможности внешней конфигурации в распределенной системе с поддержкой на стороне сервера и клиента.  Azure Spring Cloud — это центральное расположение для управления свойствами приложения во всех средах. См. сведения о [конфигурации Spring Cloud](https://spring.io/projects/spring-cloud-config). 

## <a name="bluegreen-deployments"></a>Развертывания по схеме Blue-Green

В Azure Spring Cloud поддерживаются развертывания по схеме Blue-Green, позволяющие выпускать решения и обновлять код в рабочих средах.  Эта схема управления изменениями позволяет разработчикам реализовывать функции и вносить изменения кода с возможность немедленного возврата к исходному состоянию.  Разработчики могут сосредоточиться на написании кода с использованием нескольких рабочих сред и для обновления кода или отката изменений без прерывания работы приложения.  Дополнительные сведения о промежуточных средах и развертываниях по схеме Blue-Green см. в [этой статье](spring-cloud-howto-staging-environment.md).

## <a name="cicd-pipeline-automation"></a>Автоматизация конвейера CI/CD

Azure Spring Cloud обеспечивает интеграцию с Azure DevOps с помощью Azure CLI.  Решения Azure DevOps позволяют автоматизировать интеграцию кода и развертывание в приложение Spring.  Дополнительные сведения см. в [этой статье](spring-cloud-howto-cicd.md).

## <a name="application-scaling"></a>Масштабирование приложения

С помощью Azure Spring Cloud вы можете легко масштабировать микрослужбы на панели мониторинга Azure Spring Cloud.  Количество виртуальных ЦП и объем памяти, доступной для микрослужб, можно масштабировать в соответствии со своими потребностями.  Масштабирование вступает в силу мгновенно и не требует изменения кода или повторного развертывания.  Дополнительные сведения см. в [этом учебнике](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Мониторинг приложений

Средства распределенной трассировки Spring Cloud позволяют разработчикам выполнять отладку и мониторинг сложных взаимодействий между микрослужбами в приложении.  Интегрировав [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) с [Azure Application Insights](../azure-monitor/monitor-reference.md) вы получите мощную возможность распределенной трассировки с портала Azure.  Дополнительные сведения см. в [этом учебнике](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать работу, выполните инструкции из [краткого руководства по Spring Cloud](spring-cloud-quickstart.md).

Примеры можно найти на сайте GitHub. [Примеры для Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).