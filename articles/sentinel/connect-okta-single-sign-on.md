---
title: Подключение данных единого входа Okta к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные единого входа Okta к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532033"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Подключение единого входа Okta к Azure Sentinel с помощью функции Azure

Соединитель единого входа Okta (SSO) позволяет легко подключать все журналы решений безопасности [Okta единого входа (SSO)](https://www.okta.com/products/single-sign-on/) с помощью Sentinel Azure, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. Интеграция между Okta единого входа и Azure Sentinel позволяет использовать функции Azure для извлечения данных журнала с помощью REST API.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="configure-and-connect-okta-single-sign-on"></a>Настройка и подключение единого входа Okta

Функции Azure могут интегрировать и получать события и журналы непосредственно из единого входа Okta и пересылать их в Azure Sentinel.

1. На портале Sentinel Azure щелкните **соединители данных** и выберите соединитель **единого входа Okta** .

1. Выберите **открыть страницу соединителя**.

1. Следуйте инструкциям на странице **единого входа Okta** .

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные появляются в Log Analytics в таблице **Okta_CL** .

## <a name="validate-connectivity"></a>Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить единый вход Okta к Azure Sentinel с помощью приложений-функций Azure. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.

