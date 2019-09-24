---
title: Подключение Cloud App Security данных к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Cloud App Security к Azure Sentinel.
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240121"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Подключение данных из Microsoft Cloud App Security 



Вы можете выполнять потоковую передачу журналов из [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) в метку Azure одним щелчком мыши. Это подключение позволяет передавать оповещения из Cloud App Security в метку Azure. 

## <a name="prerequisites"></a>Предварительные требования

- Пользователь с разрешениями глобального администратора или администратора безопасности

## <a name="connect-to-cloud-app-security"></a>Подключение к Cloud App Security

Если у вас уже есть Cloud App Security, убедитесь, что он [включен в сети](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Если Cloud App Security развернут и принимает данные, данные предупреждений можно легко передать в Azure Sentinel.


1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Cloud App Security** .

1. Выберите, какие журналы вы хотите передавать в Azure Sentinel. Вы можете выбрать **оповещения**. 

1. Вы можете выбрать, будут ли оповещения автоматически создавать инциденты в Azure Sentinel с Microsoft Cloud App Security автоматически. В разделе **Создание инцидентов** выберите **включить** , чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе предупреждений, созданных в подключенной службе безопасности. Затем можно изменить это правило в разделе **аналитика** , а затем — **активные правила**.

1. Щелкните **Подключить**.

1. Чтобы использовать соответствующую схему в Log Analytics для Cloud App Security предупреждений, выполните поиск по запросу **секуритялерт**.




## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить Microsoft Cloud App Security к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
