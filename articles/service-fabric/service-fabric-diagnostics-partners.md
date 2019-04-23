---
title: Партнерские решения по мониторингу Azure Service Fabric | Документация Майкрософт
description: Сведения о мониторинге платформы Azure Service Fabric с помощью решений мониторинга партнеров
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: c2f953c98e41291951f07556bd0cd441d2793d1d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796254"
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

## <a name="humio"></a>Humio

Humio — это служба сбора журналов, которое может собирать журналы из приложения и события из Service Fabric в облаке или на локальном компьютере в режиме реального времени. В дополнение к динамической наблюдаемость Humio предоставляет современные средства анализа и визуализации для просмотра и сбора аналитики из вашей системы диагностики. Humio имеет экономически эффективные тарифными планами и предназначено для масштабирования, сохраняя его счет быстрой скорости. Напрямую интегрируется с события платформы Service Fabric и данные телеметрии приложения. Дополнительные сведения об интеграции Humio и Service Fabric [здесь](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Дальнейшие действия

* Получите [представление о мониторинге и диагностике](service-fabric-diagnostics-overview.md) в Service Fabric.
* Узнайте, как [диагностировать типичные сценарии](service-fabric-diagnostics-common-scenarios.md) с помощью наших фирменных инструментов.
