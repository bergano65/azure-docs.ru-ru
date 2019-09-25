---
title: Подключение данных Azure ATP к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Azure ATP к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 764fb4c22bcce5fc5b045e68dc512243e783020e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261845"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Подключение данных из Azure Advanced Threat protection (ATP)

> [!IMPORTANT]
> Соединитель данных Azure Advanced Threat Protection в Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете выполнить потоковую передачу журналов из [Azure Advanced Threat protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) в метку Azure одним щелчком мыши.

## <a name="prerequisites"></a>Предварительные требования

- Пользователь с разрешениями глобального администратора или администратора безопасности
- Вы должны быть клиентом предварительной версии Azure ATP

## <a name="connect-to-azure-atp"></a>Подключение к Azure ATP

Убедитесь, что в [вашей сети включена](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Предварительная версия Azure ATP.
Если Azure ATP развернут и принимает данные, подозрительные оповещения можно легко передать в Azure Sentinel. Запуск потоковой передачи в Azure Sentinel может занять до 24 часов.


1. Чтобы подключить Azure ATP к Azure Sentinel, сначала необходимо включить интеграцию между Azure ATP и Microsoft Cloud App Security. Сведения о том, как это сделать, см. в статье [Интеграция Azure Advanced Threat protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Azure Advanced Threat protection (Предварительная версия)** .

1. Вы можете выбрать, будут ли оповещения из Azure ATP автоматически создавать инциденты в Azure Sentinel автоматически. В разделе **Создание инцидентов** выберите **включить** , чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе предупреждений, созданных в подключенной службе безопасности. Затем можно изменить это правило в разделе **аналитика** , а затем — **активные правила**.

1. Щелкните **Подключить**.

1. Чтобы использовать соответствующую схему в Log Analytics для оповещений Azure ATP, выполните поиск по запросу **секуритялерт**.

> [!NOTE]
> Если размер оповещений превышает 30 КБ, то Azure Sentinel прекращает отображать поле сущности в предупреждениях.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить Azure Advanced Threat protection к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

