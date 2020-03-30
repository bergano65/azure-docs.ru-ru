---
title: Подключите Forcepoint DLP к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить Forcepoint DLP к Azure Sentinel.
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
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588252"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Подключите Forcepoint DLP к Azure Sentinel

> [!IMPORTANT]
> Разъем данных Forcepoint Data Loss (DLP) в Azure Sentinel в настоящее время находится в открытом доступе. Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Разъем Forcepoint DLP позволяет автоматически экспортировать данные об инцидентах DLP в Azure Sentinel. Вы можете использовать его, чтобы получить видимость в действиях пользователей и инцидентов потери данных. Он также обеспечивает корреляцию с данными из рабочих нагрузок Azure и других каналов, а также улучшает возможности мониторинга с помощью трудовых книжек в Azure Sentinel.

> [!NOTE]
> Данные будут храниться в географическом местоположении рабочего пространства, на котором работает Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Настройка и подключение Forcepoint DLP

Навлаживание Forcepoint DLP для передачи данных об инцидентах в формате JSON в рабочее пространство Azure через REST API, как поясняется в [руководстве по интеграции Forcepoint DLP.](https://frcpnt.com/dlp-sentinel)


## <a name="find-your-data"></a>Найти свои данные

После настройки разъема Forcepoint DLP данные отославаются в журнале Analytics в **соответствии с**CustomLogs ForcepointDLPEvents_CL.


Чтобы использовать соответствующую схему в журнале Analytics для Forcepoint DLP, ищите **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Проверка подключения

Это может занять более 20 минут, пока ваши журналы начинают появляться в журнале Analytics.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить Forcepoint DLP к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.