---
title: включение файла
description: включение файла
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 059a2691c8e7905295a65daf262338ece65247d1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812639"
---
При публикации предложения в Marketplace с помощью центра партнеров вам потребуется подключить ваше предложение к системе управления отношениями с клиентами (CRM), чтобы вы могли получить контактные данные клиента сразу после того, как клиент выражает интерес или развертывает продукт.

1. **Выберите целевое назначение, куда вы хотите отправить клиентские интересы**. Поддерживаются следующие системы CRM:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) для участия клиентов
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Если ваша система CRM не поддерживается в списке выше, доступны следующие параметры, позволяющие хранить данные интереса клиента, а затем экспортировать или импортировать эти данные в систему CRM.

    * [Таблица Azure](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [Конечная точка HTTPS](../commercial-marketplace-lead-management-instructions-https.md)

2. Ознакомьтесь с документацией, приведенной выше, к выбранному назначению интереса, чтобы узнать, как настроить назначение интереса для получения интересов из предложения Marketplace. 
3. Подключайте свое предложение к месту назначения, а затем публикуете предложение в Marketplace в центре партнеров. Сведения о том, как это сделать, см. в документации, приведенной выше.
4. Убедитесь, что подключение к целевому объекту интереса настроено правильно. После того, как вы правильно настроили Назначение интереса и отправите предложение Publish в своем предложении в центре партнеров, мы пропроверяем подключение и отправим вам тестовый интерес. Перед тем как использовать предложение в рабочей среде, вы можете проверить подключение интереса, попробовав получить предложение в предварительной среде. 
5. Убедитесь, что подключение к целевому объекту-получателю остается актуальным, чтобы вы не потеряли каких-либо интересов, поэтому убедитесь, что эти подключения обновляются при каждом изменении в вашей конечной точке.
