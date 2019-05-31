---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240281"
---
1. Откройте Скачанный клиентский проект, с помощью Xcode.

2. Перейдите к [портала Azure](https://portal.azure.com/) и перейдите к мобильное приложение, которое вы создали. На `Overview` колонке найдите URL-адрес, который является общедоступной конечной точки для мобильного приложения. Пример - sitename для моей имя приложения «test123» будет https://test123.azurewebsites.net.

3. Для проекта, Swift, откройте файл `ToDoTableViewController.swift` в этой папке - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Имя приложения — `ZUMOAPPNAME`.

4. В `viewDidLoad()` метод, замените `ZUMOAPPURL` параметр с общедоступной конечной точки выше.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    становится
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Для проекта Objective-C, откройте файл `QSTodoService.m` в этой папке - ZUMOAPPNAME/ZUMOAPPNAME. Имя приложения — `ZUMOAPPNAME`.

6. В `init` метод, замените `ZUMOAPPURL` параметр с общедоступной конечной точки выше.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    становится
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Нажмите кнопку **Run** , чтобы создать проект и запустить приложение в симуляторе iOS.

8. В приложении нажмите значок плюса ( **+** ), введите осмысленный текст, например *Работа с учебником*, и нажмите кнопку сохранения. Это отправляет запрос POST на ранее развернутый внутренний сервер Azure. Сервер вносит данные из запроса в таблицу TodoItem SQL и возвращает сведения о только что сохраненных элементах в мобильное приложение. В мобильном приложении эти данные отображаются в списке.

   ![Простое приложение в iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
