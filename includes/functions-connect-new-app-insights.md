---
title: включить файл
description: включить файл
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132577"
---
Функции упрощают добавление возможностей интеграции Application Insights в приложение-функцию на [портале Azure].

1. На [портале][Azure] `Function Apps` Portal введите панель поиска в верхней части страницы, выберите приложение функции, а затем выберите не **настроенный** баннер в верхней части окна. Если вы не видите этот баннер, то ваше приложение уже включено в Application Insights.

    ![Включение Application Insights с помощью портала](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Создайте ресурс Application Insights с помощью параметров, указанных в таблице под рисунком.

   ![Создание ресурса Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Название** | Уникальное имя приложения | Проще всего использовать имя приложения-функции, которое должно быть уникальным в вашей подписке. | 
    | **Расположение** | Западная Европа | Если возможно, используйте [регион](https://azure.microsoft.com/regions/) приложения-функции или ближайший от него. |

1. Нажмите кнопку **ОК**. Ресурс Application Insights создается в той же группе ресурсов и подписке, что и приложение-функция. После создания ресурса закройте окно Application Insights.

1. В приложении-функции выберите **Параметры приложения** и прокрутите окно вниз до раздела **Параметры приложения**. Если появится параметр `APPINSIGHTS_INSTRUMENTATIONKEY`, это означает, что для приложения-функции в Azure включена интеграция с Application Insights.

[Портал Azure]: https://portal.azure.com
