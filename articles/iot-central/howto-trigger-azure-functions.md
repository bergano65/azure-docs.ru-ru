---
title: Активация функций Azure с помощью веб-перехватчиков в Azure IoT Central
description: Создание приложения-функции, которое запускается каждый раз, когда срабатывает правило в Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0d92e9bdf8ec207e5ef0e3f891c162182b5a4fff
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264851"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Активация функций Azure с помощью веб-перехватчиков в Azure IoT Central

*Этот раздел предназначен для разработчиков и администраторов.*

Используйте функции Azure для выполнения бессерверного кода на выходных данных веб-перехватчика из правил IoT Central. Не нужно подготовить виртуальную Машину или публиковать веб-приложения с помощью функций Azure, но вместо этого можно запустить этот код без сервера. Используйте функции Azure для преобразования полезных данных веб-перехватчика перед их отправкой в место назначения, такое как база данных SQL или сетка событий.

## <a name="prerequisites"></a>Технические условия

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="how-to-connect-azure-functions"></a>Подключение функций Azure

1. [Создание нового приложения-функции на портале Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Создание нового приложения-функции на портале Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Разверните приложение-функцию и выберите **кнопку +** рядом с функциями. Если эта функция является первой в свое приложение-функцию, выберите **на портале** среды разработки, а затем нажмите кнопку **Продолжить**.

    ![Выберите создание пользовательской приложения-функции](media/howto-trigger-azure-functions/customfunction.png)

3. Выберите **веб-перехватчик + API** шаблона и выберите **создать**. В этом разделе используется функция Azure на основе .NET.

    ![Выберите "Generic Webhook - C#" (Универсальный веб-перехватчик — C#)](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. В новой функции, выберите **URL-адрес функции <> / получить**, затем скопируйте и сохраните значение. Это значение используется для настройки веб-перехватчика.

    ![Получите URL-адрес функции](media/howto-trigger-azure-functions/getfunctionurl.png)

4. В IoT Central перейдите к правилу, которое вы хотите подключить к своему приложению-функции.

5. Добавьте действие веб-перехватчика. Введите **отображаемое имя** и вставьте скопированный ранее URL-адрес функции в поле **URL-адрес обратного вызова**.

    ![Введите URL-адрес функции в поле "URL-адрес обратного вызова"](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Сохраните правило. Теперь при запуске правила, веб-перехватчика вызывает для запуска приложения-функции. В приложении-функции можно выбрать **монитор** просматривать журнал вызова функции. Для просмотра журнала можно использовать Application Insights или классический вид.

    ![Мониторинг журнала вызова функции](media/howto-trigger-azure-functions/monitorfunction.PNG)

Дополнительные сведения см. в статье о [создании функции, активируемой универсальным веб-перехватчиком](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) в документации по функциям Azure.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы узнали, как настроить и использовать веб-перехватчики, ознакомьтесь с [созданием рабочих процессов в Microsoft Flow](howto-add-microsoft-flow.md).
