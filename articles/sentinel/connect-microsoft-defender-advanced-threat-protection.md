---
title: Подключение данных ATP в защитнике Майкрософт к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Microsoft защитника Advanced Threat protection к Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 19d496ebb61a3ceb47f69f661e30ab529dc64f3d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256751"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Подключайтесь к оповещениям из Microsoft защитника Advanced Threat protection 


> [!IMPORTANT]
> В настоящее время в общедоступной предварительной версии журналы приема данных Advanced Threat Protection в защитнике Майкрософт.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Вы можете выполнить потоковую передачу предупреждений из [Advanced Threat Protection в Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) в метку Azure одним щелчком мыши. Это подключение позволяет выполнять потоковую передачу предупреждений из Advanced Threat Protection в Microsoft Defender в Azure Sentinel. 

## <a name="prerequisites"></a>Предварительные требования

- Действительная лицензия для Advanced Threat Protection в защитнике Майкрософт, которая включена, как описано в разделе [проверка подготовки лицензирования и завершение настройки для службы Advanced Threat Protection в защитнике Майкрософт](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Вы должны быть администратором или администратором безопасности в клиенте Sentinel Azure.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Подключение к Advanced Threat Protection в защитнике Майкрософт

Если вы разворачиваете и принимаете ваши данные, вы можете легко перепотокировать оповещения в Azure Sentinel, если вы развернете расширенную защиту от угроз Microsoft Defender.


1. В поле Sentinel Azure выберите **соединители данных**, щелкните плитку **Advanced Threat Protection в защитнике (Майкрософт** ) и выберите **открыть страницу соединителя**.
1. Щелкните **Подключить**. 
1. Чтобы использовать соответствующую схему в Log Analytics оповещений ATP в защитнике, выполните поиск по запросу **секуритялерт** , а **имя поставщика** — **мдатп**.




## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить ATP Microsoft Defender к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
