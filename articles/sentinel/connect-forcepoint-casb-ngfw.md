---
title: Подключение продуктов Forcepoint к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить продукты Forcepoint к Azure Sentinel.
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
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588235"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Подключите продукты Forcepoint к Azure Sentinel

> [!IMPORTANT]
> Разъем данных продуктов Forcepoint в Azure Sentinel в настоящее время находится в открытом доступе. Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


В этой статье объясняется, как подключить продукты Forcepoint к Azure Sentinel. 

Разъемы данных Forcepoint позволяют подключить журналы Безопасности доступа к облачным технологиям Forcepoint Cloud Access Broker и Forcepoint Next Generation С помощью журналов Azure Sentinel. Таким образом, вы можете автоматически экспортировать журналы, определяемые пользователями, в Azure Sentinel в режиме реального времени. Разъем обеспечивает обогащенную видимость действий пользователей, записанных продуктами Forcepoint. Это также позволяет дальнейшую корреляцию с данными из рабочих нагрузок Azure и других каналов, а также улучшает возможности мониторинга с помощью трудовых книжек внутри Azure Sentinel.

> [!NOTE]
> Данные будут храниться в географическом местоположении рабочего пространства, на котором работает Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Переадресованные журналы продуктов Forcepoint агенту Syslog 

Нанастройка продукта Forcepoint для переадресовывания сообщений Syslog в формате CEF в рабочее пространство Azure через агента Syslog.

1. Настройка продукта Forcepoint для интеграции Azure Sentinel, описанная в следующих руководствах по установке:
 - [Руководство по интеграции Forcepoint CASB](https://frcpnt.com/casb-sentinel)
 - [Руководство по интеграции Forcepoint NGFW](https://frcpnt.com/ngfw-sentinel)

2. Поиск CommonSecurityLog для использования соответствующей схемы в журнале Analytics с названием DeviceVendor содержит "Forcepoint". 

3. Продолжить [STEP 3: Проверка подключения](connect-cef-verify.md).



## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить продукты Forcepoint к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).

- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.