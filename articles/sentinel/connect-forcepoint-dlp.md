---
title: Подключение Forcepoint DLP к Azure Sentinel | Документация Майкрософт
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588252"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Подключение Forcepoint DLP к Azure Sentinel

> [!IMPORTANT]
> Соединитель данных защиты от потери данных Forcepoint в Azure Sentinel в настоящее время находится в общедоступной предварительной версии. Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Соединитель DLP Forcepoint позволяет автоматически экспортировать данные об инциденте защиты от потери данных в метку Azure. Его можно использовать для получения информации о действиях пользователей и инцидентах потери данных. Кроме того, она позволяет выполнять корреляцию с данными из рабочих нагрузок Azure и других веб-каналов, а также улучшает возможности мониторинга с помощью книг в Azure Sentinel.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="configure-and-connect-forcepoint-dlp"></a>Настройка и подключение DLP Forcepoint

Настройте Forcepoint DLP, чтобы пересылать данные об инцидентах в формате JSON в рабочую область Azure с помощью REST API, как описано в разделе [Forcepoint об интеграции с DLP по защиты от потери](https://frcpnt.com/dlp-sentinel)данных.


## <a name="find-your-data"></a>Поиск данных

После настройки соединителя DLP Forcepoint данные отображаются в Log Analytics в разделе CustomLogs **ForcepointDLPEvents_CL**.


Чтобы использовать соответствующую схему в Log Analytics DLP Forcepoint, выполните поиск **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить Forcepoint DLP к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.