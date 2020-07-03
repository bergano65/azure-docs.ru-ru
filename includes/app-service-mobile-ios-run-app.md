---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240281"
---
1. Откройте загруженный клиентский проект с помощью Xcode.

2. Перейдите на [портал Azure](https://portal.azure.com/) и перейдите к созданного вами мобильному приложению. На `Overview` лезвии ищите URL-адрес, который является общедоступной точкой зрения для вашего мобильного приложения. Пример - название сайта для моего приложения имя https://test123.azurewebsites.net"test123" будет .

3. Для проекта Swift откройте файл `ToDoTableViewController.swift` в этой папке - ЗУМОАППНАМ/ЗУМОАППНАМ/ТоДоТаблицВЬюКонтроллер.быстро. Имя приложения — `ZUMOAPPNAME`.

4. В `viewDidLoad()` методе `ZUMOAPPURL` замените параметр общедоступной конечной точкой выше.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    becomes
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Для проекта «Объектив-С» `QSTodoService.m` откройте файл в этой папке – «ЗУМОАППНАМЕ»/ЗУМОАППНАМЕ. Имя приложения — `ZUMOAPPNAME`.

6. В `init` методе `ZUMOAPPURL` замените параметр общедоступной конечной точкой выше.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    becomes
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Нажмите кнопку **Run** , чтобы создать проект и запустить приложение в симуляторе iOS.

8. В приложении щелкните значок plus ()**+** введите значимый текст, например, *«Завершите учебник»,* а затем нажмите кнопку «Сохранить». Это отправляет запрос POST на ранее развернутый внутренний сервер Azure. Сервер вносит данные из запроса в таблицу TodoItem SQL и возвращает сведения о только что сохраненных элементах в мобильное приложение. В мобильном приложении эти данные отображаются в списке.

   ![Простое приложение в iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
