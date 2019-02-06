---
title: Azure Application Insights. Функции, поддерживаемые в службе "Функции Azure" | Документация Майкрософт
description: Поддерживаемые функции Application Insights для службы "Функции Azure"
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 06feece050835b2b9188eb702210770b44a6b49c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185822"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights для функций, поддерживаемых в службе "Функции Azure"

Служба "Функции Azure" предлагает [встроенную интеграцию](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) с Application Insights, которая доступна через интерфейс ILogger. Ниже приведен список функций, поддерживаемых в настоящее время. Ознакомьтесь с руководством по службе "Функции Azure", чтобы [приступить к работе](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Поддерживаемые функции

| Функции Azure                       | V1                | Версия 2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Пакет SDK Application Insights для .NET**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **Автоматический сбор**        |                 |                   |               
| &bull; Запросы                     | Yes             | Yes               | 
| &bull; Исключения                   | Yes             | Yes               | 
| &bull; Зависимости                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; Служебная шина|                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Yes               | 
| | | | 
| **Поддерживаемые функции**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Yes             | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; Безопасный канал управления|                 | Yes               | 
| &bull; Выборка                     | Yes             | Yes               | 
| &bull; Пакет пульса                   |                 | Yes               | 
| | | | 
| **Корреляция**                       |                   |                   |               
| &bull; Служебная шина                     |                   | Yes               | 
| &bull; EventHub                       |                   | Yes               | 
| | | | 
| **Можно настроить**                      |                   |                   |           
| &bull;Полностью настраивается.<br/>Инструкции см. в комментариях [в этой статье](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852).<br/>Сведения обо всех вариантах ASP.NET Core см. в статье [Custom Configuration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) (Настраиваемая конфигурация).               |                   | Yes                   | 


## <a name="live-metrics--secure-control-channel"></a>Интерактивные метрики и безопасный канал управления

Указываемые вами пользовательские критерии фильтра передаются в компонент Live Metrics в пакете SDK для Application Insights. Фильтры могут содержать конфиденциальные сведения, например идентификаторы клиентов. Вы можете защитить канал, используя секретный ключ API. Инструкции см. в разделе [Защита канала управления](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel).

## <a name="sampling"></a>Выборка

Служба "Функции Azure" включает в своей конфигурации выборку по умолчанию. Дополнительные сведения см. в разделе [о настройке выборки](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).
