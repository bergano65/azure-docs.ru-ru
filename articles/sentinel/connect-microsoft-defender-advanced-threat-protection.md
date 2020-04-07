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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756354"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Подключение предупреждений от Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> В настоящее время в открытом доступе находится заглатывается оповещение о защите расширенных угроз Microsoft Defender Advanced Threat Protection.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Разъем [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) позволяет передавать оповещения из Microsoft Defender Advanced Threat Protection в Azure Sentinel. Это позволит вам более тщательно анализировать события безопасности в вашей организации и создавать игровые книги для эффективного и немедленного реагирования.

## <a name="prerequisites"></a>Предварительные требования

- Вы должны иметь действительную лицензию для Microsoft Defender Advanced Threat Protection, как описано в [развертывании Microsoft Defender ATP.](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing) 
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
