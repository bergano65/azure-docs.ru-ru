---
title: Подключение данных Okta Single Sign-On к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Okta Single Sign-On к Azure Sentinel.
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
ms.openlocfilehash: 05a9b8009d896a2ee87df3e1c4493d249a887566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083928"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Подключение Okta Single Sign-On к Azure Sentinel с помощью функции Azure

> [!IMPORTANT]
> Соединитель данных Okta Single Sign-On в Azure Sentinel в настоящее время находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Соединитель Okta Single Sign-On (SSO) позволяет легко подключать все журналы решений безопасности [Okta single Sign-On (SSO)](https://www.okta.com/products/single-sign-on/) с помощью Sentinel, для просмотра панелей мониторинга, создания пользовательских оповещений и улучшения расследования. Интеграция между Okta Single Sign-On и Azure Sentinel позволяет использовать функции Azure для извлечения данных журнала с помощью REST API.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="configure-and-connect-okta-single-sign-on"></a>Настройка и подключение Okta Single Sign-On

Функции Azure могут интегрировать и запрашивать события и журналы непосредственно из Okta Single Sign-On и пересылать их в Azure Sentinel.

1. На портале Sentinel Azure щелкните **соединители данных** и выберите соединитель **единого входа Okta** .

1. Выберите **открыть страницу соединителя**.

1. Следуйте инструкциям на странице **единого входа Okta** .

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные появляются в Log Analytics в таблице **Okta_CL** .

## <a name="validate-connectivity"></a>Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить Okta Single Sign-On к Sentinel Azure с помощью приложений функций Azure. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.

