---
title: Подключение данных АТФ Azure к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные АТФ Azure к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588592"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Подключение данных из расширенной защиты от угроз Azure (ATP)

> [!IMPORTANT]
> Разъем данных Расширенной защиты угроз Azure в Azure Sentinel в настоящее время находится в открытом доступе.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете одним щелчком мыши передавать журналы из [расширенной защиты от угроз Azure](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) в Azure Sentinel.

## <a name="prerequisites"></a>Предварительные требования

- Пользователь с разрешениями глобального администратора или администратора безопасности
- Вы должны быть клиентом предварительного просмотра Azure ATP и обеспечить интеграцию между Azure ATP и Microsoft Cloud App Security. Для получения дополнительной [Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration)информации см.

## <a name="connect-to-azure-atp"></a>Подключение к Azure ATP

Убедитесь, что версия предварительного просмотра Azure ATP [включена в сеть.](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)
Если Azure ATP развернут и попадает в ваши данные, подозрительные оповещения можно легко передавать в Azure Sentinel. Начало потоковой передачи оповещений в Azure Sentinel может занять до 24 часов.


1. Чтобы подключить Azure ATP к Azure Sentinel, необходимо сначала включить интеграцию между Azure ATP и Microsoft Cloud App Security. Для получения информации о [Azure Advanced Threat Protection integration](https://docs.microsoft.com/cloud-app-security/aatp-integration)том, как это сделать, см.

1. В Azure Sentinel выберите **разъемы данных,** а затем щелкните плитку **Azure Advanced Threat Protection (Preview).**

1. Можно выбрать, хотите ли оповещения от Azure ATP автоматически создавать инциденты в Azure Sentinel автоматически. В разделе **Создать инциденты** выберите **Разрешить**, чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе оповещений, созданных в подключенной службе безопасности. Затем это правило можно изменить в разделе **Аналитика**, а затем **Активные правила**.

1. Нажмите кнопку **Подключить**.

1. Чтобы использовать соответствующую схему в журнале Analytics для предупреждений АТБ Azure, ищите **SecurityAlert.**

> [!NOTE]
> Если оповещения превышают 30 кВ, Azure Sentinel перестает отображать поле Сущностей в предупреждениях.

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить расширенную защиту угроз Azure к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

