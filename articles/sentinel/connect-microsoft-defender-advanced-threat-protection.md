---
title: Подключите данные Microsoft Defender ATP к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные Microsoft Defender Advanced Threat Protection к Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588218"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Подключение предупреждений от Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> В настоящее время в открытом доступе находится вход в журналы Microsoft Defender Advanced Threat Protection.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Вы можете передавать оповещения из [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) в Azure Sentinel одним щелчком мыши. Это соединение позволяет передавать оповещения из Microsoft Defender Advanced Threat Protection в Azure Sentinel. 

## <a name="prerequisites"></a>Предварительные требования

- Действительная лицензия для Microsoft Defender Advanced Threat Protection, которая включена, как описано в [подготовке лицензирования validate, и полной настройке для Microsoft Defender Advanced Threat Protection.](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing) 
- Вы должны быть администратором или администратором безопасности в арендаторе Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Подключение к Microsoft Defender Расширенная защита от угроз

Если корпорация Майкрософт защитник расширенной защиты от угроз развернута и попадает в ваши данные, оповещения могут быть легко переданы в Azure Sentinel.


1. В Azure Sentinel выберите **разъемы данных,** щелкните плитку **Microsoft Defender Advanced Threat Protection** и выберите **страницу Open connector.**
1. Нажмите кнопку **Подключить**. 
1. Чтобы использовать соответствующую схему в журнале Analytics для предупреждений Defender ATP, поиск **SecurityAlert** и **имя поставщика** **MDATP**.




## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить АТФ Microsoft Defender к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
