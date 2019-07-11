---
title: включение файла
description: включение файла
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c519fc2db020b8df22275c6b276c6ec23d10b1c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608338"
---
Функции упрощают добавление возможностей интеграции Application Insights в приложение-функцию на [портал Azure].

1. На [портале][портал azure] выберите **Все службы > Приложения-функции**. Выберите приложение-функцию и щелкните баннер **Application Insights** в верхней части окна.

    ![Включение Application Insights с помощью портала](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Создайте ресурс Application Insights с помощью параметров, указанных в таблице под рисунком.

   ![Создание ресурса Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя** | Уникальное имя приложения | Проще всего использовать имя приложения-функции, которое должно быть уникальным в вашей подписке. | 
    | **Местоположение.** | Западная Европа | Если возможно, используйте [регион](https://azure.microsoft.com/regions/) приложения-функции или ближайший от него. |

1. Нажмите кнопку **ОК**. Ресурс Application Insights создается в той же группе ресурсов и подписке, что и приложение-функция. После завершения создания закройте окно Application Insights.

1. В приложении-функции выберите **Параметры приложения** и прокрутите окно вниз до раздела **Параметры приложения**. Когда появится параметр `APPINSIGHTS_INSTRUMENTATIONKEY`, это означает, что для приложения-функции в Azure включена интеграция с Application Insights.

[портал Azure]: https://portal.azure.com
