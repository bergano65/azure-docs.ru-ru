---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66137030"
---
Если в Python возникает ошибка во время запуска приложения, возвращается только простая страница ошибки (например, "The page cannot be displayed because an internal server error has occurred" (Не удалось отобразить страницу, так как произошла внутренняя ошибка сервера)).

Чтобы включить запись ошибок в приложении Python, сделайте следующее:

1. На портале Azure перейдите к своему веб-приложению и выберите **Параметры**.
2. На вкладке **Параметры** щелкните **Параметры приложения**.
3. В разделе **Параметры приложения** введите следующую пару "ключ — значение":
    * Ключ: WSGI_LOG
    * Значение: D:\home\site\wwwroot\logs.txt (введите собственное имя файла)

Теперь ошибки должны отображаться в файле logs.txt в папке wwwroot.
