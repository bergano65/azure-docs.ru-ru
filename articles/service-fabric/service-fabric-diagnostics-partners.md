---
title: Партнерские решения по мониторингу Azure Service Fabric | Документация Майкрософт
description: Сведения о мониторинге платформы Azure Service Fabric с помощью решений мониторинга партнеров
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: 45e6de347cbb3ecfcc45ca20ab03805359207565
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444673"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Партнерские решения по мониторингу Azure Service Fabric

В этой статье рассказывается о возможностях мониторинга приложений, кластеров и инфраструктуры Service Fabric с помощью удобных партнерских решений. С каждым из указанных ниже партнеров мы сотрудничали для создания интегрированных предложений для Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Интеграция с Dynatrace обеспечивает множество готовых возможностей для мониторинга кластеров Service Fabric. Установка агента Dynatrace OneAgent на экземпляры VMSS позволяет использовать счетчики производительности и топологию развертывания Service Fabric на уровне приложения. Dynatrace также является отличным решением для локального мониторинга. См. другие функции, перечисленные в [объявления](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) и [инструкции](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) для включения Dynatrace в кластере. 

## <a name="datadog"></a>Datadog

Datadog предлагает расширение для VMSS для экземпляров Windows и Linux. С помощью Datadog можно собирать данные журналов событий Windows, включая события платформы Service Fabric в Windows. Ознакомьтесь с инструкциями по отправке данных диагностики в Datadog [здесь](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Интеграция Service Fabric с AppDynamics осуществляется на уровне приложения. Изменяя переменные среды и используя пакеты NuGet AppDynamics, вы можете отправлять данные телеметрии приложений в AppDynamics. См. [инструкции](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) по интеграции приложений .NET Service Fabric с AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic — это средство управления производительностью приложений, которое хорошо интегрируется с приложениями Service Fabric. Вы можете установить пакеты NuGet New Relic и добавить определенные переменные среды в файлы манифестов для отправки данных телеметрии приложений в New Relic. Ознакомьтесь с этими [инструкциями](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) по включению телеметрии New Relic для приложений .NET Service Fabric.

## <a name="elk"></a>ELK 

Стек ELK представляет собой набор технологий с открытым исходным кодом: Elasticsearch, Logstash и Kibana. Используя эти решения, можно собирать, хранить и анализировать данные мониторинга и диагностики Service Fabric. [Здесь](service-fabric-tutorial-java-elk.md) есть учебник для собственных приложений Java Service Fabric. 


## <a name="next-steps"></a>Дальнейшие действия

* Получите [представление о мониторинге и диагностике](service-fabric-diagnostics-overview.md) в Service Fabric.
* Узнайте, как [диагностировать типичные сценарии](service-fabric-diagnostics-common-scenarios.md) с помощью наших фирменных инструментов.
