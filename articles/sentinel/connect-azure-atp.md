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
ms.openlocfilehash: ebb727055296ba7886a9307ada113ab5a6e0c9e0
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240192"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp---preview"></a>Подключение данных из Azure Advanced Threat protection (ATP) — Предварительная версия




Вы можете выполнить потоковую передачу журналов из [Azure Advanced Threat protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) в метку Azure одним щелчком мыши.

## <a name="prerequisites"></a>Предварительные требования

- Пользователь с разрешениями глобального администратора или администратора безопасности
- Вы должны быть клиентом предварительной версии Azure ATP

## <a name="connect-to-azure-atp"></a>Подключение к Azure ATP

Убедитесь, что в [вашей сети включена](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Предварительная версия Azure ATP.
Если Azure ATP развернут и принимает данные, подозрительные оповещения можно легко передать в Azure Sentinel. Запуск потоковой передачи в Azure Sentinel может занять до 24 часов.


1. Чтобы подключить Azure ATP к Azure Sentinel, сначала необходимо включить интеграцию между Azure ATP и Microsoft Cloud App Security. Сведения о том, как это сделать, см. в статье [Интеграция Azure Advanced Threat protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Azure ATP** .

1. Вы можете выбрать, будут ли оповещения из Azure ATP автоматически создавать инциденты в Azure Sentinel автоматически. В разделе **Создание инцидентов** выберите **включить** , чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе предупреждений, созданных в подключенной службе безопасности. Затем можно изменить это правило в разделе **аналитика** , а затем — **активные правила**.

1. Щелкните **Подключить**.

1. Чтобы использовать соответствующую схему в Log Analytics для оповещений Azure ATP, выполните поиск по запросу **секуритялерт**.

> [!NOTE]
> Если размер оповещений превышает 30 КБ, то Azure Sentinel прекращает отображать поле сущности в предупреждениях.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить Azure Advanced Threat protection к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

