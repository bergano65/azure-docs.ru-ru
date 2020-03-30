---
title: Подключение данных Центра безопасности Azure к Azure Sentinel
description: Узнайте, как подключить данные Центра безопасности Azure к Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588541"
---
# <a name="connect-data-from-azure-security-center"></a>Подключение данных из Центра безопасности Azure





Azure Sentinel позволяет подключать оповещения из [Центра безопасности Azure](../security-center/security-center-intro.md) и передавать их в Azure Sentinel. 

## <a name="prerequisites"></a>Предварительные требования

- Для экспорта оповещений из Центра безопасности Azure необходимо иметь роль читателя безопасности в подписке на потоковые журналы.

- Необходимо, чтобы [стандартный уровень Azure Security Center](../security-center/security-center-pricing.md) работал по подписке. В противом случае [обновите подписку до стандартной.](https://azure.microsoft.com/pricing/details/security-center/)



## <a name="connect-to-azure-security-center"></a>Подключение к Центру безопасности Azure

1. В Azure Sentinel выберите **разъемы данных,** а затем щелкните плитку **Azure Security Center.**

1. Справа щелкните **Connect** рядом с каждой подпиской, оповещения которой вы хотите передавать в Azure Sentinel. Убедитесь в том, чтобы обновить каждую подписку до стандартного уровня Azure Security Center для потоковой передачи оповещений в Azure Sentinel.

1. Можно выбрать, хотите ли оповещения из Центра безопасности Azure автоматически генерировать инциденты в Azure Sentinel автоматически. В разделе **Создать инциденты** выберите **Разрешить**, чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе оповещений, созданных в подключенной службе безопасности. Затем это правило можно изменить в разделе **Аналитика**, а затем **Активные правила**.

3. Нажмите кнопку **Подключить**.

4. Чтобы использовать соответствующую схему в журнале Analytics для предупреждений Центра безопасности Azure, ищите **SecurityAlert.**

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить Центр безопасности Azure к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
