---
title: Подключение данных Azure ATP к предварительной версии Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Azure ATP к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599158"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Подключение данных из Azure Advanced Threat protection (ATP)

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Вы можете выполнить потоковую передачу журналов из [Azure Advanced Threat protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) в метку Azure одним щелчком мыши.

## <a name="prerequisites"></a>Предварительные требования

- Пользователь с разрешениями глобального администратора или администратора безопасности
- Вы должны быть клиентом предварительной версии Azure ATP

## <a name="connect-to-azure-atp"></a>Подключение к Azure ATP

Убедитесь, что в [вашей сети включена](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Предварительная версия Azure ATP.
Если Azure ATP развернут и принимает данные, подозрительные оповещения можно легко передать в Azure Sentinel. Запуск потоковой передачи в Azure Sentinel может занять до 24 часов.


1. Чтобы подключить Azure ATP к Azure Sentinel, сначала необходимо включить интеграцию между Azure ATP и Microsoft Cloud App Security. Сведения о том, как это сделать, см. в статье [Интеграция Azure Advanced Threat protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Azure ATP** .

2. Щелкните **Подключить**.

6. Чтобы использовать соответствующую схему в Log Analytics для оповещений Azure ATP, выполните поиск по запросу **секуритялерт**.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить Azure Advanced Threat protection к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

