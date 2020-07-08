---
title: Подключение данных ATP в защитнике Майкрософт к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Microsoft защитника Advanced Threat protection к Azure Sentinel.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756354"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Подключайтесь к оповещениям из Microsoft защитника Advanced Threat protection 


> [!IMPORTANT]
> В настоящее время в общедоступной предварительной версии оповещения о повышении защиты от угроз Microsoft Defender
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Соединитель [Advanced Threat protection (Майкрософт](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) ) позволяет выполнять потоковую передачу предупреждений из Advanced Threat Protection в защитнике Майкрософт в Azure Sentinel. Это позволит вам более детально анализировать события безопасности в Организации и создавать модули PlayBook для эффективного и немедленного реагирования.

## <a name="prerequisites"></a>Предварительные условия

- У вас должна быть действительная лицензия для Advanced Threat Protection в защитнике Майкрософт, как описано в разделе [Настройка развертывания ATP в защитнике Майкрософт](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Вы должны быть администратором или администратором безопасности в клиенте Sentinel Azure.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Подключение к Advanced Threat Protection в защитнике Майкрософт

Если вы разворачиваете и принимаете ваши данные, вы можете легко перепотокировать оповещения в Azure Sentinel, если вы развернете расширенную защиту от угроз Microsoft Defender.


1. В поле Sentinel Azure выберите **соединители данных**, щелкните плитку **Advanced Threat Protection в защитнике (Майкрософт** ) и выберите **открыть страницу соединителя**.
1. Нажмите кнопку **Соединить**. 
1. Чтобы использовать соответствующую схему в Log Analytics оповещений ATP в защитнике, выполните поиск по запросу **секуритялерт** , а **имя поставщика** — **мдатп**.




## <a name="next-steps"></a>Дальнейшие шаги
В этом документе вы узнали, как подключить ATP Microsoft Defender к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
