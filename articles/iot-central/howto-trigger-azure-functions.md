---
title: Активация функций Azure с помощью веб-перехватчиков в Azure IoT Central
description: Создание приложения-функции, которое запускается каждый раз, когда срабатывает правило в Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 694c259472bf7e18f0ae3d424acf5b5cfb7ea7ab
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294260"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Активация функций Azure с помощью веб-перехватчиков в Azure IoT Central

*Этот раздел предназначен для разработчиков и администраторов.*

Используйте функции Azure для выполнения бессерверного кода на выходных данных веб-перехватчика из правил IoT Central. Вам не нужно подготавливать виртуальную машину или публиковать веб-приложение для использования функций Azure, но вместо этого вы можете выполнить этот код без сервера. Используйте функции Azure для преобразования полезных данных веб-перехватчика перед их отправкой в место назначения, такое как база данных SQL или сетка событий. 

## <a name="prerequisites"></a>Предварительные требования
+ Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="how-to-connect-azure-functions"></a>Подключение функций Azure

1. [Создайте приложение-функцию на портале Azure.](https://ms.portal.azure.com/#create/Microsoft.FunctionApp)

    ![Создайте приложение-функцию на портале Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Разверните приложение-функцию и нажмите кнопку **+** рядом с элементом "Функции". Если это первая функция в приложении-функции, выберите **Пользовательская функция**. Откроется полный набор шаблонов функций.
    
    ![Выберите создание пользовательской приложения-функции](media/howto-trigger-azure-functions/customfunction.png)

3. В поле поиска введите **"generic"** ("универсальный") и выберите нужный язык для шаблона триггера универсального веб-перехватчика. В этой статье используется функция C#. 

    ![Выберите "Generic Webhook - C#" (Универсальный веб-перехватчик — C#)](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. В новой функции щелкните **</> Получить URL-адрес функции**, а затем скопируйте и сохраните значения. Это значение будет использоваться для настройки веб-перехватчика. 

    ![Получите URL-адрес функции](media/howto-trigger-azure-functions/getfunctionurl.png)
4. В IoT Central перейдите к правилу, которое вы хотите подключить к своему приложению-функции.

5. Добавьте действие веб-перехватчика. Введите **отображаемое имя** и вставьте скопированный ранее URL-адрес функции в поле **URL-адрес обратного вызова**.

    ![Введите URL-адрес функции в поле "URL-адрес обратного вызова"](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Сохраните правило. Теперь при срабатывании правила веб-перехватчик будет запускать приложение-функцию. В приложении-функции щелкните **Монитор**, чтобы просмотреть журнал вызова функции. Для просмотра журнала можно использовать Application Insights или классический вид.

    ![Мониторинг журнала вызова функции](media/howto-trigger-azure-functions/monitorfunction.PNG)

Дополнительные сведения см. в статье о [создании функции, активируемой универсальным веб-перехватчиком](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) в документации по функциям Azure. 

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы узнали, как настроить и использовать веб-перехватчики, ознакомьтесь с [созданием рабочих процессов в Microsoft Flow](howto-add-microsoft-flow.md).
