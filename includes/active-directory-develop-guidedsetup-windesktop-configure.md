---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: fb521fc9d4927a953cdd66948101969dfc102de4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843208"
---
## <a name="register-your-application"></a>Регистрация приложения
Приложение можно зарегистрировать одним из двух способов.

### <a name="option-1-express-mode"></a>Вариант 1. Экспресс-режим
Чтобы быстро зарегистрировать приложение, сделайте следующее:
1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Выбор **Добавить приложение**.

3. В поле **Имя приложения** введите имя приложения.

4. Обязательно установите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.

5. Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="option-2-advanced-mode"></a>Вариант 2. Расширенный режим
Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение, сделайте следующее:
1. Если вы еще не зарегистрировали приложение, перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app).

2. Выбор **Добавить приложение**.

3. В поле **Имя приложения** введите имя приложения. 

4. Обязательно снимите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.

5. Выберите **Добавление платформы**, **Собственное приложение**, а затем нажмите кнопку **Сохранить**.

6. В поле **Идентификатор приложения**, скопируйте идентификатор GUID.

7. Перейдите в Visual Studio, откройте файл *App.xaml.cs*, а затем замените `your_client_id_here` только что зарегистрированным ы скопированным идентификатором приложения.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
