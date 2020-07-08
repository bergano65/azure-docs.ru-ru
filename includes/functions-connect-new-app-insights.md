---
title: Включить имя файла
description: включить файл
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731048"
---
Функции Azure упрощают добавление Application Insights интеграции в приложение-функцию из [портал Azure].

1. На[портале Azure] [портал Azure]найдите и выберите **приложение функции**, а затем выберите приложение функции. 

1. Выберите **Application Insights не настроен** баннер в верхней части окна. Если вы не видите этот баннер, возможно, приложение уже включено Application Insights.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Включение Application Insights с помощью портала":::

1. Разверните узел **изменить ресурс** и создайте Application Insights ресурс с помощью параметров, указанных в следующей таблице.  

    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Новое название ресурса** | Уникальное имя приложения | Проще всего использовать имя приложения-функции, которое должно быть уникальным в вашей подписке. | 
    | **Расположение** | Западная Европа | Если возможно, используйте [регион](https://azure.microsoft.com/regions/) приложения-функции или ближайший от него. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Создание ресурса Application Insights":::

1. Нажмите кнопку **Применить**. 

   Ресурс Application Insights создается в той же группе ресурсов и подписке, что и приложение-функция. После создания ресурса закройте окно Application Insights.

1. В приложении функции выберите пункт **Конфигурация** в разделе **Параметры**, а затем выберите **Параметры приложения**. Если появится параметр `APPINSIGHTS_INSTRUMENTATIONKEY`, это означает, что для приложения-функции в Azure включена интеграция с Application Insights.

[портале Azure]: https://portal.azure.com
