---
title: Подключение данных больших IP-адресов F5 к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные больших IP-адресов F5 к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: ff5a8f940348c36ca2a19fb31a173b2dfc4f4d6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514138"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Подключение устройства F5 с большим IP-адресом 

> [!IMPORTANT]
> Соединитель F5 больших IP-адресов в Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Соединитель F5 BIG-IP позволяет легко подключать все журналы больших IP-адресов F5 с помощью Azure Sentinel, просматривать книги, создавать пользовательские оповещения и улучшать исследование. Это позволяет получить более подробные сведения о сети организации и улучшить возможности обеспечения безопасности. Интеграция между F5 BIG-IP и Sentinel Azure использует REST API.


> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="configure-and-connect-f5-big-ip"></a>Настройка и подключение F5 BIG-IP 

F5 BIG-IP может интегрировать и экспортировать журналы непосредственно в Azure Sentinel.

1. На портале Sentinel Azure щелкните **соединители данных** и выберите **F5 BIG-IP** , а затем **откройте страницу соединителя**. 
1. Чтобы подключиться к серверу F5 BIG-IP, необходимо опубликовать объявление JSON в конечной точке API системы. Инструкции по выполнению этой задачи см. в статье [Интеграция команды F5 BIG-IP с Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. На странице соединителя F5 BIG-IP скопируйте идентификатор рабочей области и первичный ключ и вставьте их, как описано в разделе [потоковая передача данных в Azure log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. После выполнения инструкций F5 BIG-IP на странице соединителя Sentinel Azure отображаются подключенные типы данных.
1. Чтобы использовать соответствующую схему в Log Analytics для событий больших IP-адресов F5, выполните поиск по запросу **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**и **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить метку F5 BIG-IP к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

