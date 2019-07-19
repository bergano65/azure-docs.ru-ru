---
title: Подключение данных Symantec AWS к предварительной версии Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Symantec AWS к Azure Sentinel.
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
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 246d4cd7d64554ae575767cdba2e26066ad1720d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295618"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Подключение Sentinel Azure к AWS Клаудтраил

Используйте соединитель AWS для потоковой передачи всех событий AWS Клаудтраил в Azure Sentinel. Этот процесс подключения делегирует доступ для маркеров Azure к журналам ресурсов AWS, создавая доверительные отношения между AWS Клаудтраил и Sentinel. Это выполняется в AWS путем создания роли, которая предоставляет разрешение на метку Azure для доступа к журналам AWS.

## <a name="prerequisites"></a>Предварительные требования

У вас должно быть разрешение на запись в рабочей области "Sentinel" Azure.

> [!NOTE]
> Azure Sentinel собирает события Клаудтраил из всех регионов. Не рекомендуется выполнять потоковую передачу событий из одного региона в другой.

## <a name="connect-aws"></a>Подключение AWS 


1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните строку **Amazon Web Services** в таблице и в области AWS справа щелкните **открыть страницу соединителя**.

1. Следуйте инструкциям в разделе **Конфигурация** , выполнив следующие действия.
 
1.  В консоли Amazon Web Services в разделе **безопасность, удостоверение & соответствие требованиям**выберите **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Выберите **роли** и щелкните **создать роль**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Выберите **другую учетную запись AWS.** В поле **идентификатор учетной записи** введите **идентификатор учетной записи Майкрософт** (**123412341234**), который можно найти на странице соединителя AWS на портале Sentinel Azure.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Убедитесь, что выбран параметр **требовать внешний идентификатор** , а затем введите внешний идентификатор (идентификатор рабочей области), который можно найти на странице соединителя AWS на портале Sentinel Azure.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  В разделе **политика разрешений присоединения** выберите **авсклаудтраилреадонлякцесс**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Введите тег (необязательно).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Затем введите **имя роли** и нажмите кнопку **создать роль** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  В списке роли выберите созданную роль.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Скопируйте **ARN роли**. На портале Sentinel Azure на экране соединителя Amazon Web Services вставьте его в поле **роль для добавления** и нажмите кнопку **Добавить**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Чтобы использовать соответствующую схему в Log Analytics для событий AWS, выполните поиск по запросу **авсклаудтраил**.



## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить AWS Клаудтраил к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

