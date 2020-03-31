---
title: Подключите данные отдела по технологиям Squadra в Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные отдела Squadra Technologies secRMM к Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588116"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Подключите данные отдела Squadra Technologies в Azure Sentinel 

> [!IMPORTANT]
> Разъем данных Squadra Technologies Security Removable Media Manager (secRMM) в Azure Sentinel в настоящее время находится в открытом доступе.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Разъем сетрм-разъем Squadra Technologies позволяет легко подключить журналы решений безопасности Squadra Technologies secRMM с Azure Sentinel. Он позволяет просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. Этот разъем дает вам представление о USB съемных событий хранения. Интеграция между Squadra Technologies secRMM и Azure Sentinel использует REST API.


> [!NOTE]
> Данные будут храниться в географическом местоположении рабочего пространства, на котором работает Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Налажить и соединять Squadra Technologies secRMM 

Squadra Technologies secRMM может интегрировать и экспортировать журналы непосредственно в Azure Sentinel.
1. На портале Azure Sentinel щелкните разъемы данных и выберите secRMM Squadra Technologies, а затем откройте страницу разъема.

2. Выполните действия, изложенные в [руководстве по технологиям Squadra для Azure Sentinel,](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) чтобы получить данные Squadra secRMM в Azure Sentinel.   


## <a name="find-your-data"></a>Найти свои данные

После успешного соединения данные отославается в журнале Analytics в соответствии с secRMM_CL CustomLogs.
Чтобы использовать соответствующую схему в журнале Analytics для squadra Technologies secRMM, ищите secRMM_CL.

## <a name="validate-connectivity"></a>Проверка подключения
Это может занять более 20 минут, пока ваши журналы начинают появляться в журнале Analytics. 


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить отдел Squadra Technologies к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.

