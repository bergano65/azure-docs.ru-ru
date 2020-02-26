---
title: Подключение данных центра безопасности Azure к Azure Sentinel
description: Узнайте, как подключить данные центра безопасности Azure к Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588541"
---
# <a name="connect-data-from-azure-security-center"></a>Подключение данных из центра безопасности Azure





Azure Sentinel позволяет подключать оповещения из [центра безопасности Azure](../security-center/security-center-intro.md) и выполнять их потоковую передачу в Azure Sentinel. 

## <a name="prerequisites"></a>Предварительные требования

- Чтобы экспортировать оповещения из центра безопасности Azure, необходимо иметь роль "читатель безопасности" в подписке на журналы, которые вы выполняете в вашем потоке.

- В подписке должен быть установлен [уровень "Стандартный" центра безопасности Azure](../security-center/security-center-pricing.md) . Если нет, [обновите подписку до уровня "Стандартный](https://azure.microsoft.com/pricing/details/security-center/)".



## <a name="connect-to-azure-security-center"></a>Подключение к центру безопасности Azure

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Центр безопасности Azure** .

1. В правой части щелкните **Подключиться** рядом с каждой подпиской, чьи оповещения необходимо передать в Azure Sentinel. Не забудьте обновить каждую подписку до уровня "Стандартный" центра безопасности Azure для потоковой передачи оповещений в Azure Sentinel.

1. Вы можете выбрать, должны ли оповещения в центре безопасности Azure автоматически создавать инциденты в Azure Sentinel. В разделе **Создать инциденты** выберите **Разрешить**, чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе оповещений, созданных в подключенной службе безопасности. Затем это правило можно изменить в разделе **Аналитика**, а затем **Активные правила**.

3. Нажмите кнопку **Соединить**.

4. Чтобы использовать соответствующую схему в Log Analytics для оповещений центра безопасности Azure, выполните поиск по запросу **секуритялерт**.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить центр безопасности Azure к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
