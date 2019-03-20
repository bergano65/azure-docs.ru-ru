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
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: 154af3988084792331db082b99cae0ae06126f1b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242179"
---
# <a name="collect-data-from-azure-advanced-threat-protection-atp"></a>Сбор данных из Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Azure Sentinel сейчас находится в общедоступной предварительной версии.
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


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel Azure Advanced Threat Protection. Дополнительные сведения о Azure Sentinel, см. в разделе со следующими статьями:
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

