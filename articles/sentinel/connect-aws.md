---
title: Подключите AWS CloudTrail к Azure Sentinel (ru) Документы Майкрософт
description: Узнайте, как подключить данные AWS CloudTrail к Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588660"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Подключение Azure Sentinel к AWS CloudTrail

Используйте разъем AWS для передачи всех событий AWS CloudTrail в Azure Sentinel. Этот процесс подключения делегирует доступ azure Sentinel к журналам ресурсов AWS, создавая доверительные отношения между AWS CloudTrail и Azure Sentinel. Это достигается на AWS, создавая роль, которая дает разрешение Azure Sentinel на доступ к вашим журналам AWS.

## <a name="prerequisites"></a>Предварительные требования

У вас должно быть разрешение на запись в рабочем пространстве Azure Sentinel.

> [!NOTE]
> Azure Sentinel собирает события CloudTrail из всех регионов. Рекомендуется не передавать события из одного региона в другой.

## <a name="connect-aws"></a>Подключение AWS 


1. В Azure Sentinel выберите **разъемы данных,** а затем выберите строку **Web Services Amazon** в таблице и в панели AWS вправо, нажмите **Открытая страница разъема.**

1. Следуйте инструкциям в **соответствии с конфигурацией,** используя следующие шаги.
 
1.  В консоли Amazon Web Services под **безопасностью, идентификацией & соответствие**требованиям выберите **IAM.**

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Выберите **роли** и выберите **Создать роль**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Выберите **другую учетную запись AWS.** В поле **идентификатора учетной записи** введите **идентификатор учетной записи Майкрософт** **(12341234),** который можно найти на странице разъема AWS на портале Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Убедитесь, что **требуется внешний идентификатор** выбран, а затем и введите внешний идентификатор (Workspace ID), который можно найти на странице разъема AWS на портале Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  В **соответствии** с политикой разрешения Присоединения выберите **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Введите тег (необязательно).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Затем введите **имя роли** и выберите кнопку **«Создание роли».**

    ![AWS7](./media/connect-aws/aws-7.png)

1.  В списке ролей выберите созданную роль.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Копировать **роль ARN**. На портале Azure Sentinel на экране разъема Amazon Web Services вставьте его в **роль, чтобы добавить** поле и нажмите **Добавить**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Чтобы использовать соответствующую схему в журнале Analytics для событий AWS, выполнить поиск **AWSCloudTrail.**



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить AWS CloudTrail к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.

