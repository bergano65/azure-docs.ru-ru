---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240317"
---
1. Откройте проект при помощи **Android Studio**, выберите **Import project (Eclipse ADT, Gradle, etc.)** (Импорт проекта (Eclipse ADT, Gradle и т. д.)). Обязательно используйте эту функцию импорта, чтобы избежать ошибок JDK.

2. Откройте `ToDoActivity.java` файл в этой папке - ЗУМОАПППЕ/приложение/src/main/java/com/example/zumoappname. Имя приложения — `ZUMOAPPNAME`.

3. Перейдите на [портал Azure](https://portal.azure.com/) и перейдите к созданного вами мобильному приложению. На `Overview` лезвии ищите URL-адрес, который является общедоступной точкой зрения для вашего мобильного приложения. Пример - название сайта для моего приложения имя https://test123.azurewebsites.net"test123" будет .

4. В `onCreate()` методе `ZUMOAPPURL` замените параметр общедоступной конечной точкой выше.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    becomes
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Нажмите кнопку **Run 'app'** (Запустить приложение), чтобы создать проект и запустить приложение в симуляторе Android.

4. В приложении введите значимый текст, например, *заполните учебник,* а затем нажмите кнопку «Добавить». Это отправляет запрос POST на ранее развернутый внутренний сервер Azure. Сервер вносит данные из запроса в таблицу TodoItem SQL и возвращает сведения о только что сохраненных элементах в мобильное приложение. В мобильном приложении эти данные отображаются в списке.
    ![Быстрый запуск Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)