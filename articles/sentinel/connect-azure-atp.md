---
title: Подключение данных Azure ATP к Azure Sentinel | Документация Майкрософт
description: Узнайте, как выполнять потоковую передачу журналов из Azure Advanced Threat protection (ATP) в Azure Sentinel одним щелчком мыши.
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
ms.openlocfilehash: f58c38ccfa234752a80c05c300d245c6c9e97cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559165"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Подключение данных из Azure Advanced Threat protection (ATP)

> [!IMPORTANT]
> Соединитель данных Azure Advanced Threat Protection в Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете выполнить потоковую передачу журналов из [Azure Advanced Threat protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) в метку Azure одним щелчком мыши.

## <a name="prerequisites"></a>Предварительные условия

- Пользователь с разрешениями глобального администратора или администратора безопасности
- Вы должны быть предварительной версией клиента Azure ATP и включить интеграцию между Azure ATP и Microsoft Cloud App Security. Дополнительные сведения см. в статье [Интеграция с расширенной защитой Azure](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Подключение к Azure ATP

Убедитесь, что в [вашей сети включена](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Предварительная версия Azure ATP.
Если Azure ATP развернут и принимает данные, подозрительные оповещения можно легко передать в Azure Sentinel. Запуск потоковой передачи в Azure Sentinel может занять до 24 часов.


1. Чтобы подключить Azure ATP к Azure Sentinel, сначала необходимо включить интеграцию между Azure ATP и Microsoft Cloud App Security. Сведения о том, как это сделать, см. в статье [Интеграция Azure Advanced Threat protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Azure Advanced Threat protection (Предварительная версия)** .

1. Вы можете выбрать, будут ли оповещения из Azure ATP автоматически создавать инциденты в Azure Sentinel автоматически. В разделе **Создать инциденты** выберите **Разрешить**, чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе оповещений, созданных в подключенной службе безопасности. Затем это правило можно изменить в разделе **Аналитика**, а затем **Активные правила**.

1. Нажмите кнопку **Соединить**.

1. Чтобы использовать соответствующую схему в Log Analytics для оповещений Azure ATP, выполните поиск по запросу **секуритялерт**.

> [!NOTE]
> Если размер оповещений превышает 30 КБ, то Azure Sentinel прекращает отображать поле сущности в предупреждениях.

## <a name="next-steps"></a>Дальнейшие шаги
В этом документе вы узнали, как подключить Azure Advanced Threat protection к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

