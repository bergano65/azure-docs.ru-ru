---
title: Собирать данные Azure ATP в предварительной версии Sentinel Azure | Документация Майкрософт
description: Дополнительные сведения о сборе данных Azure ATP в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: rkarlin
ms.openlocfilehash: 5254e60b9b7c38e5f4534e90f8aabe938aef99b2
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574949"
---
# <a name="collect-data-from-azure-advanced-threat-protection-atp"></a>Сбор данных из Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Можно осуществлять потоковую передачу журналов из [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) в Azure Sentinel одним щелчком.

## <a name="prerequisites"></a>Технические условия

- Пользователь с глобальным администратором или разрешения администратора безопасности
- Необходимо быть пользователем закрытой предварительной версии Azure ATP

## <a name="connect-to-azure-atp"></a>Подключение к Azure ATP

Убедитесь, что в частной предварительной версии Azure ATP [включена в сети](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Если развертывается Azure ATP и приема данных, подозрительные оповещения можно легко настроить потоковую передачу в Azure Sentinel. Может занять до 24 часов для оповещений начать потоковую передачу в Azure Sentinel.



1. В Azure Sentinel, выберите **сбора данных** и нажмите кнопку **Azure ATP** плитку.

2. Щелкните **Подключить**.

6. Чтобы использовать соответствующей схемы в Log Analytics для оповещений Azure ATP, поиск **SecurityAlert**.

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel Azure Advanced Threat Protection. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

