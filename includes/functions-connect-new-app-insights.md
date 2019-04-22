---
title: включение файла
description: включение файла
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59497358"
---
Функции упрощают добавление возможностей интеграции Application Insights в приложение-функцию на [портале Azure].

1. На [портале][Azure] выберите **Все службы > Приложения-функции**. Выберите приложение-функцию и щелкните баннер **Application Insights** в верхней части окна.

    ![Включение Application Insights с помощью портала](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Создайте ресурс Application Insights с помощью параметров, указанных в таблице под рисунком.

   ![Создание ресурса Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **ИМЯ** | Уникальное имя приложения | Проще всего использовать имя приложения-функции, которое должно быть уникальным в вашей подписке. | 
    | **Расположение** | Западная Европа | Если возможно, используйте [регион](https://azure.microsoft.com/regions/) приложения-функции или ближайший от него. |

1. Нажмите кнопку **ОК**. Ресурс Application Insights создается в той же группе ресурсов и подписке, что и приложение-функция. После завершения создания закройте окно Application Insights.

1. В приложении-функции выберите **Параметры приложения** и прокрутите окно вниз до раздела **Параметры приложения**. Когда появится параметр `APPINSIGHTS_INSTRUMENTATIONKEY`, это означает, что для приложения-функции в Azure включена интеграция с Application Insights.

[Портал Azure]: https://portal.azure.com
