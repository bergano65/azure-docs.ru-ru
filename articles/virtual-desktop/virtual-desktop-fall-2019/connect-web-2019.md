---
title: Подключение веб-клиента Windows Virtual Desktop (классическая модель) к Azure
description: Как подключиться к виртуальному рабочему столу Windows (классическая модель) с помощью веб-клиента.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: efe97c86ebfac8e130489b3105a97302866d6822
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87270385"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Подключение к виртуальному рабочему столу Windows (классическая модель) с помощью веб-клиента

>[!IMPORTANT]
>Это содержимое относится к виртуальному рабочему столу Windows (классическая модель), который не поддерживает Azure Resource Manager объекты виртуальных рабочих столов Windows. Если вы пытаетесь управлять Azure Resource Manager объектами виртуальных рабочих столов Windows, см. [эту статью](../connect-web.md).

Веб-клиент позволяет получать доступ к ресурсам Виртуального рабочего стола Windows из веб-браузера без необходимости устанавливать программное обеспечение.

>[!NOTE]
>В настоящее время веб-клиент не поддерживает мобильные ОС.

## <a name="supported-operating-systems-and-browsers"></a>Поддерживаемые операционные системы и браузеры

Несмотря на то, что подойдет любой браузер, поддерживающий HTML5, корпорация Майкрософт официально поддерживает следующие операционные системы и браузеры.

| Браузер           | Поддерживаемая ОС                     | Примечания               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari;      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Версии 55 или более поздней |
| Google Chrome     | Windows, macOS, Linux, ОС Chrome |                     |

## <a name="access-remote-resources-feed"></a>Доступ к веб-каналу удаленных ресурсов

В браузере перейдите к веб-клиенту Виртуального рабочего стола Windows по адресу <https://rdweb.wvd.microsoft.com/webclient> и выполните вход с помощью учетной записи пользователя.

>[!NOTE]
>Если вы используете виртуальный рабочий стол Windows с Azure Resource Managerной интеграцией, подключитесь к ресурсам по адресу <https://rdweb.wvd.microsoft.com/arm/webclient> .

>[!NOTE]
>Если вы уже вошли, используя другую учетную запись Azure Active Directory, отличную от той, которую вы хотите использовать для Виртуального рабочего стола Windows, необходимо выйти из системы или использовать вкладку браузера в режиме конфиденциального просмотра.

После входа в систему отобразится список ресурсов. Чтобы запустить ресурсы, выберите их, как при работе с обычным приложением, на вкладке **Все ресурсы**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании веб-клиента см. в статье [Начало работы с веб-клиентом](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
