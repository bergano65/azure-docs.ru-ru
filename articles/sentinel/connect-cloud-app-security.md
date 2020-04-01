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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422141"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Подключение данных из Microsoft Cloud App Security 



Разъем [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) позволяет передавать оповещения и [журналы облачных дисков](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) от MCAS в Azure Sentinel. Это позволит вам получать видимость в ваших облачных приложениях, получать сложную аналитику для выявления и борьбы с киберугрозами, а также контролировать, как ваши данные перемещается.

## <a name="prerequisites"></a>Предварительные требования

- Пользователь должен прочитать и написать разрешения на рабочем пространстве.
- Пользователь должен иметь разрешения Глобального администратора или администратора безопасности в отношении арендатора рабочего пространства.
- Чтобы поток журналов Cloud Discovery в Azure Sentinel, [включите Azure Sentinel в качестве SIEM в Microsoft Cloud App Security.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> В настоящее время в открытом доступе находится журнал Cloud Discovery.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Подключение к Cloud App Security

Если у вас уже есть Cloud App Security, убедитесь, что оно [включено в вашей сети.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
При развертывании и глотании данных Cloud App Security можно легко передавать данные оповещения в Azure Sentinel.


1. Из меню навигации Azure Sentinel выберите **разъемы данных.** Из списка разъемов щелкните плитку **Microsoft Cloud App Security,** а затем кнопку **"Открытая страница разъема"** в правом нижнем правом.

1. Выберите, какие журналы вы хотите передавать в Azure Sentinel; Вы можете выбрать **журналы оповещения** и **облачные обнаружения** (предварительный просмотр). 

1. Щелкните **Применить изменения**.

1. Чтобы использовать соответствующую схему в журнале Analytics для `SecurityAlert` оповещений об облачных приложениях, введите окно запроса. Для схемы журналов облачных `McasShadowItReporting`журналов типа «Облако обнаружения» введите тип —

> [!NOTE]
> Cloud Discovery помогает обнаруживать и выявлять тенденции, агрегируя соединения пользователей с облачными приложениями, лежащие в основе данных.
>
> Поскольку данные Cloud Discovery агрегируются в день, имейте в виду, что последние данные за 24 часа не будут отражены в Azure Sentinel. В случае, если исследование низкого уровня требует более непосредственных данных, оно должно быть сделано непосредственно в исходном приборе или службе, где находятся необработанные данные.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить Microsoft Cloud App Security к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Начать обнаруживать угрозы с помощью Azure Sentinel, используя [встроенные](tutorial-detect-threats.md) или [пользовательские](tutorial-detect-threats-custom.md) правила.
