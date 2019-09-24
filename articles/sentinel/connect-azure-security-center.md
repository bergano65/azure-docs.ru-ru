---
title: Подключение данных центра безопасности Azure к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные центра безопасности Azure к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240221"
---
# <a name="connect-data-from-azure-security-center"></a>Подключение данных из центра безопасности Azure





Azure Sentinel позволяет подключать оповещения из [центра безопасности Azure](../security-center/security-center-intro.md) и выполнять их потоковую передачу в Azure Sentinel. 

## <a name="prerequisites"></a>Предварительные требования

- Если вы хотите экспортировать оповещения из центра безопасности Azure, вы должны быть участником подписки, в которой выполняется потоковая передача журналов.

- В подписке должен быть установлен [уровень "Стандартный" центра безопасности Azure](../security-center/security-center-pricing.md) . Если нет, [обновите подписку до уровня "Стандартный](https://azure.microsoft.com/pricing/details/security-center/)".

- Для подключения к каждой подписке, которую вы хотите подключить, необходимо войти в систему с правами глобального администратора или администратора безопасности.


## <a name="connect-to-azure-security-center"></a>Подключение к центру безопасности Azure

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Центр безопасности Azure** .

1. В правой части щелкните **Подключиться** рядом с каждой подпиской, чьи оповещения необходимо передать в Azure Sentinel. Не забудьте обновить каждую подписку до уровня "Стандартный" центра безопасности Azure для потоковой передачи оповещений в Azure Sentinel.

1. Вы можете выбрать, должны ли оповещения в центре безопасности Azure автоматически создавать инциденты в Azure Sentinel. В разделе **Создание инцидентов** выберите **включить** , чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе предупреждений, созданных в подключенной службе безопасности. Затем можно изменить это правило в разделе **аналитика** , а затем — **активные правила**.

3. Щелкните **Подключить**.

4. Чтобы использовать соответствующую схему в Log Analytics для оповещений центра безопасности Azure, выполните поиск по запросу **секуритялерт**.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить центр безопасности Azure к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
