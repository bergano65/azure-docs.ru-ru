---
title: Подключение к виртуальному рабочему столу Windows с помощью веб-клиента в Azure
description: Сведения о подключении к Виртуальному рабочему столу Windows с помощью веб-клиента.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fdbd0ddb33785655cada2600acdecdced4aeb2f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283441"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Подключение к виртуальному рабочему столу Windows с помощью веб-клиента

>[!IMPORTANT]
>Это содержимое применяется к виртуальному рабочему столу Windows с Azure Resource Manager объектами виртуальных рабочих столов Windows. Если вы используете Windows Virtual Desktop (классическая модель) без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/connect-web-2019.md).

Веб-клиент позволяет получить доступ к ресурсам Виртуального рабочего стола Windows из веб-браузера без необходимости устанавливать программное обеспечение.

>[!NOTE]
>В настоящее время веб-клиент не поддерживает мобильные ОС.

## <a name="supported-operating-systems-and-browsers"></a>Поддерживаемые операционные системы и браузеры

Несмотря на то, что подойдет любой браузер, поддерживающий HTML5, корпорация Майкрософт официально поддерживает следующие операционные системы и браузеры.

| Браузер           | Поддерживаемая ОС                     | Примечания               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Версия 11 или более поздняя |
| Apple Safari;      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Версии 55 или более поздней |
| Google Chrome     | Windows, macOS, Linux, ОС Chrome |                     |

## <a name="access-remote-resources-feed"></a>Доступ к веб-каналу удаленных ресурсов

В браузере перейдите к версии веб-клиента Виртуального рабочего стола Windows, интегрированной с Azure Resource Manager, по адресу <https://rdweb.wvd.microsoft.com/arm/webclient> и выполните вход с помощью своей учетной записи пользователя.

>[!NOTE]
>Если вы используете Windows Virtual Desktop (классическая модель) без интеграции Azure Resource Manager, подключитесь к ресурсам по адресу <https://rdweb.wvd.microsoft.com/webclient> .

>[!NOTE]
>Если вы уже вошли, используя другую учетную запись Azure Active Directory, отличную от той, которую вы хотите использовать для Виртуального рабочего стола Windows, необходимо выйти из системы или использовать вкладку браузера в режиме конфиденциального просмотра.

После входа в систему отобразится список ресурсов. Чтобы запустить ресурсы, выберите их, как при работе с обычным приложением, на вкладке **Все ресурсы**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании веб-клиента см. в статье [Начало работы с веб-клиентом](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
