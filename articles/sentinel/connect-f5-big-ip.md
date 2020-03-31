---
title: Подключите данные F5 BIG-IP к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные F5 BIG-IP к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588286"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Подключите f5 BIG-IP прибор 

> [!IMPORTANT]
> Разъем данных F5 BIG-IP в Azure Sentinel в настоящее время находится в открытом доступе.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Разъем F5 BIG-IP позволяет легко подключить все журналы F5 BIG-IP с Azure Sentinel, просматривать трудовые книжки, создавать пользовательские оповещения и улучшать исследования. Это дает вам более глубокое представление о сети вашей организации и улучшает возможности работы службы безопасности. Интеграция между F5 BIG-IP и Azure Sentinel использует REST API.


> [!NOTE]
> Данные будут храниться в географическом местоположении рабочего пространства, на котором работает Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Налаживание и подключение F5 BIG-IP 

F5 BIG-IP может интегрировать и экспортировать журналы непосредственно в Azure Sentinel.

1. На портале Azure Sentinel щелкните **разъемы данных** и выберите **F5 BIG-IP,** а затем **откройте страницу разъема.** 
1. Чтобы подключить F5 BIG-IP, необходимо разместить декларацию JSON в конечную точку API системы. Для получения инструкций о том, как это сделать, [см. Интеграция F5 BIG-IP с Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Со страницы разъема F5 BIG-IP скопируйте идентификатор Workspace ID и primary Key и вставьте их по указанию в соответствии с [потоковым итогом в Azure Log Analytics.](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)
1. После завершения инструкций F5 BIG-IP на странице разъема Azure Sentinel вы увидите связанные типы данных.
1. Использовать соответствующую схему в журнале Analytics для событий F5 BIG-IP, поиск **F5Telemetry_LTM_CL,** **F5Telemetry_system_CL**и **F5Telemetry_ASM_CL.**


## <a name="validate-connectivity"></a>Проверка подключения

Это может занять более 20 минут, пока ваши журналы начинают появляться в журнале Analytics. 



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить F5 BIG-IP к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.


