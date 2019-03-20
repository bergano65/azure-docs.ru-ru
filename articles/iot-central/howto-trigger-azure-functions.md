---
title: Активация функций Azure с помощью веб-перехватчиков в Azure IoT Central
description: Создание приложения-функции, которое запускается каждый раз, когда срабатывает правило в Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 512956d2de0f9a838cc6378345a334e489d1d120
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306873"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Активация функций Azure с помощью веб-перехватчиков в Azure IoT Central

*Этот раздел предназначен для разработчиков и администраторов.*

Используйте функции Azure для выполнения бессерверного кода на выходных данных веб-перехватчика из правил IoT Central. Вам не нужно подготавливать виртуальную машину или публиковать веб-приложение для использования функций Azure, но вместо этого вы можете выполнить этот код без сервера. Используйте функции Azure для преобразования полезных данных веб-перехватчика перед их отправкой в место назначения, такое как база данных SQL или сетка событий.

## <a name="prerequisites"></a>Технические условия

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="how-to-connect-azure-functions"></a>Подключение функций Azure

1. [Создание нового приложения-функции на портале Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Создайте приложение-функцию на портале Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Разверните приложение-функцию и выберите **кнопку +** рядом с функциями. Если это первая функция в приложении-функции, выберите **Пользовательская функция**. Откроется полный набор шаблонов функций.

    ![Выберите создание пользовательской приложения-функции](media/howto-trigger-azure-functions/customfunction.png)

3. В поле поиска введите **"generic"** ("универсальный") и выберите нужный язык для шаблона триггера универсального веб-перехватчика. В этой статье используется функция C#. 

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
