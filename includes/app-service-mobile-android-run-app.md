---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240317"
---
1. Откройте проект при помощи **Android Studio**, выберите **Import project (Eclipse ADT, Gradle, etc.)** (Импорт проекта (Eclipse ADT, Gradle и т. д.)). Обязательно используйте эту функцию импорта, чтобы избежать ошибок JDK.

2. Откройте файл `ToDoActivity.java` в этой папке - ZUMOAPPNAME/приложения/src/main/java/com/пример/zumoappname. Имя приложения — `ZUMOAPPNAME`.

3. Перейдите к [портала Azure](https://portal.azure.com/) и перейдите к мобильное приложение, которое вы создали. На `Overview` колонке найдите URL-адрес, который является общедоступной конечной точки для мобильного приложения. Пример - sitename для моей имя приложения «test123» будет https://test123.azurewebsites.net.

4. В `onCreate()` метод, замените `ZUMOAPPURL` параметр с общедоступной конечной точки выше.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    становится
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Нажмите кнопку **Run 'app'** (Запустить приложение), чтобы создать проект и запустить приложение в симуляторе Android.

4. В приложении введите содержательный текст, например *Работа с учебником* , и нажмите кнопку «Add» (Добавить). Это отправляет запрос POST на ранее развернутый внутренний сервер Azure. Сервер вносит данные из запроса в таблицу TodoItem SQL и возвращает сведения о только что сохраненных элементах в мобильное приложение. В мобильном приложении эти данные отображаются в списке.
    ![Краткое руководство Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)