---
title: Включить имя файла
description: включить файл
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121583"
---
Функции упрощают добавление возможностей интеграции Application Insights в приложение-функцию на [портале Azure].

1. На[портале Azure] [портал Azure]найдите и выберите **приложение функции**, а затем выберите приложение функции. 

1. Выберите **Application Insights не настроен** баннер в верхней части окна. Если вы не видите этот баннер, приложение уже имеет Application Insights включено.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Включение Application Insights с помощью портала":::

1. Создайте ресурс Application Insights с помощью параметров, указанных в таблице под рисунком.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Создание ресурса Application Insights":::

    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **имя**; | Уникальное имя приложения | Проще всего использовать имя приложения-функции, которое должно быть уникальным в вашей подписке. | 
    | **Расположение** | Западная Европа | Если возможно, используйте [регион](https://azure.microsoft.com/regions/) приложения-функции или ближайший от него. |

1. Нажмите кнопку **Применить**. Ресурс Application Insights создается в той же группе ресурсов и подписке, что и приложение-функция. После создания ресурса закройте окно Application Insights.

1. Вернитесь в приложение функции, выберите **Параметры**  >  **Конфигурация**, а затем выберите **Параметры приложения**. Если появится параметр `APPINSIGHTS_INSTRUMENTATIONKEY`, это означает, что для приложения-функции в Azure включена интеграция с Application Insights.

[Портал Azure]: https://portal.azure.com
