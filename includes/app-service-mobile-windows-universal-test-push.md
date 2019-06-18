---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 70e0d766fe3c9669912ce9f13f729fb1c757a26f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140483"
---
1. Щелкните правой кнопкой мыши проект Магазина Windows, выберите пункт **Назначить запускаемым проектом**и нажмите клавишу F5, чтобы запустить приложение Магазина Windows.

    После запуска приложения выполняется регистрация устройства для получения push-уведомлений.
2. Остановите приложение Магазина Windows и повторите предыдущий шаг для приложения Магазина Windows Phone.

    Теперь оба устройства зарегистрированы для получения push-уведомлений.

3. Запустите приложение Магазина Windows и введите текст в поле **Insert a TodoItem** (Вставить TodoItem), после чего нажмите кнопку **Сохранить**.

    Обратите внимание: после завершения вставки как приложение Магазина Windows, так и приложение Windows Phone получают push-уведомление из WNS. Уведомление отображается на устройстве Windows Phone, даже если приложение не запущено.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
