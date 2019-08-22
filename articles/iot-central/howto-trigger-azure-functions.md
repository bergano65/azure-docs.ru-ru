---
title: Активация функций Azure с помощью веб-перехватчиков в Azure IoT Central
description: Создание приложения-функции, которое запускается каждый раз, когда срабатывает правило в Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e7c0f0abdf4a96f4af904f76549bdebd62b803cd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877324"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Активация функций Azure с помощью веб-перехватчиков в Azure IoT Central

*Этот раздел предназначен для разработчиков и администраторов.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Используйте функции Azure для выполнения бессерверного кода на выходных данных веб-перехватчика из правил IoT Central. Вам не нужно подготавливать виртуальную машину или публиковать веб-приложение для использования функций Azure, но вместо этого можно выполнить этот серверный код. Используйте функции Azure для преобразования полезных данных веб-перехватчика перед их отправкой в место назначения, такое как база данных SQL или сетка событий.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="how-to-connect-azure-functions"></a>Подключение функций Azure

1. [Создайте новое приложение-функцию в портал Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Создание нового приложения-функции в портал Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Разверните приложение функции и нажмите **кнопку +** рядом с пунктом функции. Если эта функция является первой в приложении-функции, выберите **на портале в** качестве среды разработки и нажмите кнопку **продолжить**.

    ![Выберите создание пользовательской приложения-функции](media/howto-trigger-azure-functions/customfunction.png)

3. Выберите **веб-перехватчик + шаблон API** и щелкните **создать**. В этом разделе используется функция Azure на основе .NET.

    ![Выберите "Generic Webhook - C#" (Универсальный веб-перехватчик — C#)](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. В новой функции выберите **</> получить URL-адрес функции**, затем скопируйте и сохраните значение. Это значение используется для настройки веб-перехватчика.

    ![Получите URL-адрес функции](media/howto-trigger-azure-functions/getfunctionurl.png)

4. В IoT Central перейдите к правилу, которое вы хотите подключить к своему приложению-функции.

5. Добавьте действие веб-перехватчика. Введите **отображаемое имя** и вставьте скопированный ранее URL-адрес функции в поле **URL-адрес обратного вызова**.

    ![Введите URL-адрес функции в поле "URL-адрес обратного вызова"](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Сохраните правило. Теперь при активации правила веб-перехватчик вызывает приложение-функцию для выполнения. В приложении функции можно выбрать **монитор** , чтобы просмотреть журнал вызовов функции. Для просмотра журнала можно использовать Application Insights или классический вид.

    ![Мониторинг журнала вызова функции](media/howto-trigger-azure-functions/monitorfunction.PNG)

Дополнительные сведения см. в статье о [создании функции, активируемой универсальным веб-перехватчиком](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) в документации по функциям Azure.

## <a name="next-steps"></a>Следующие шаги
Теперь, когда вы узнали, как настроить и использовать веб-перехватчики, ознакомьтесь с [созданием рабочих процессов в Microsoft Flow](howto-add-microsoft-flow.md).
