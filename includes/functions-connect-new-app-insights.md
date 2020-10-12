---
title: включить файл
description: включить файл
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84731048"
---
Функции Azure упрощают добавление возможностей интеграции Application Insights в приложение-функцию на [портале Azure].

1. На [портале Azure][портал Azure] выполните поиск **приложения-функции** и выберите его. 

1. Щелкните баннер **Application Insights не настроено** в верхней части окна. Если вы не видите этот баннер, возможно, для приложения уже включено Application Insights.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Включение Application Insights с помощью портала":::

1. Разверните пункт **Изменить ресурс** и создайте ресурс Application Insights с помощью параметров, указанных в следующей таблице.  

    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Новое название ресурса** | Уникальное имя приложения | Проще всего использовать имя приложения-функции, которое должно быть уникальным в вашей подписке. | 
    | **Расположение** | Западная Европа | Если возможно, используйте [регион](https://azure.microsoft.com/regions/) приложения-функции или ближайший от него. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Включение Application Insights с помощью портала":::

1. Нажмите кнопку **Применить**. 

   Ресурс Application Insights создается в той же группе ресурсов и подписке, что и приложение-функция. После создания ресурса закройте окно Application Insights.

1. В приложении-функции выберите **Конфигурация** в разделе **Параметры** и выберите **Параметры приложения**. Если появится параметр `APPINSIGHTS_INSTRUMENTATIONKEY`, это означает, что для приложения-функции в Azure включена интеграция с Application Insights.

[Портал Azure]: https://portal.azure.com
