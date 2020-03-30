---
title: Подключение данных облачной безопасности приложений к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные облачной безопасности к Azure Sentinel.
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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588371"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Подключение данных из Microsoft Cloud App Security 



Вы можете одним щелчком мыши передавать журналы из [Облачной безопасности приложений](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) в Azure Sentinel. Это соединение позволяет передавать оповещения из Облачной безопасности приложений в Azure Sentinel. 

## <a name="prerequisites"></a>Предварительные требования

- Пользователь с разрешениями глобального администратора или администратора безопасности
- Чтобы поток журналов Cloud Discovery в Azure Sentinel, [включите Azure Sentinel в качестве SIEM в Microsoft Cloud App Security.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> В настоящее время в открытом доступе находится журнал Cloud Discovery.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Подключение к Cloud App Security

Если у вас уже есть Cloud App Security, убедитесь, что оно [включено в вашей сети.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
При развертывании и глотании данных Cloud App Security можно легко передавать данные оповещения в Azure Sentinel.


1. В Azure Sentinel выберите **разъемы данных,** щелкните плитку **Cloud App Security** и выберите **страницу Open connector.**

1. Выберите, какие журналы вы хотите передавать в Azure Sentinel, вы можете выбрать **журналы оповещения** и **облачные обнаружения** (предварительный просмотр). 

1. Нажмите кнопку **Подключить**.

1. Чтобы использовать соответствующую схему в журнале Analytics для оповещений об облачной безопасности приложений, ищите **SecurityAlert.**




## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить Microsoft Cloud App Security к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
