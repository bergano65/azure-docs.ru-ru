---
title: Партнерские решения по мониторингу Azure Service Fabric
description: Узнайте, как контролировать приложения, кластеры и инфраструктуру Службы обслуживания Azure с помощью решений для мониторинга партнеров.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645724"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Партнерские решения по мониторингу Azure Service Fabric

В этой статье рассказывается о возможностях мониторинга приложений, кластеров и инфраструктуры Service Fabric с помощью удобных партнерских решений. С каждым из указанных ниже партнеров мы сотрудничали для создания интегрированных предложений для Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Интеграция с Dynatrace обеспечивает множество готовых возможностей для мониторинга кластеров Service Fabric. Установка агента Dynatrace OneAgent на экземпляры VMSS позволяет использовать счетчики производительности и топологию развертывания Service Fabric на уровне приложения. Dynatrace также является отличным решением для локального мониторинга. Ознакомьтесь с другими функциями, перечисленными в [объявлении,](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) и [инструкциями, позволяющими](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) Включить Dynatrace в вашем кластере. 

## <a name="datadog"></a>Datadog

Datadog предлагает расширение для VMSS для экземпляров Windows и Linux. С помощью Datadog можно собирать данные журналов событий Windows, включая события платформы Service Fabric в Windows. Ознакомьтесь с инструкциями по отправке данных диагностики в Datadog [здесь](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Интеграция Service Fabric с AppDynamics осуществляется на уровне приложения. Изменяя переменные среды и используя пакеты NuGet AppDynamics, вы можете отправлять данные телеметрии приложений в AppDynamics. См. [инструкции](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) по интеграции приложений .NET Service Fabric с AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic — это средство управления производительностью приложений, которое хорошо интегрируется с приложениями Service Fabric. Вы можете установить пакеты NuGet New Relic и добавить определенные переменные среды в файлы манифестов для отправки данных телеметрии приложений в New Relic. Ознакомьтесь с этими [инструкциями](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) по включению телеметрии New Relic для приложений .NET Service Fabric.

## <a name="elk"></a>ELK 

Стек ELK представляет собой набор технологий с открытым исходным кодом: Elasticsearch, Logstash и Kibana. Используя эти технологии в сочетании, вы можете собирать, хранить и анализировать данные мониторинга и диагностики сервисной ткани. [Здесь](service-fabric-tutorial-java-elk.md) есть учебник для собственных приложений Java Service Fabric. 

## <a name="humio"></a>Хумио

Humio — это служба сбора журналов, которая может собирать журналы из приложений и событий из Service Fabric в облаке или в помещении в режиме реального времени. В дополнение к живой наблюдению, Humio предлагает самые современные возможности анализа и визуализации для просмотра и сбора информации из вашей диагностики. Humio имеет экономически эффективные планы ценообразования и построен для масштабирования, сохраняя при этом его осветления быстрой скорости. Он напрямую интегрируется с событиями платформы Service Fabric и телеметрией приложений. Вы можете прочитать больше об интеграции Humio и Service Fabric [здесь](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Дальнейшие действия

* Получите [представление о мониторинге и диагностике](service-fabric-diagnostics-overview.md) в Service Fabric.
* Узнайте, как [диагностировать типичные сценарии](service-fabric-diagnostics-common-scenarios.md) с помощью наших фирменных инструментов.
