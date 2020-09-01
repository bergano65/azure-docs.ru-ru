---
title: Подключение к виртуальному рабочему столу Windows с помощью веб-клиента в Azure
description: Сведения о подключении к Виртуальному рабочему столу Windows с помощью веб-клиента.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ca5a591d168e18181a29cf1c00a7d1fead54595
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226052"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Подключение к виртуальному рабочему столу Windows с помощью веб-клиента

>[!IMPORTANT]
>Это содержимое применимо к Виртуальному рабочему столу Windows с объектами Azure Resource Manager для Виртуального рабочего стола Windows. Если вы используете Виртуальный рабочий стол Windows (классический) без объектов Azure Resource Manager, ознакомьтесь с [этой статьей](./virtual-desktop-fall-2019/connect-web-2019.md).

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
>
> Если вы используете портал US Gov, используйте <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Если вы уже вошли, используя другую учетную запись Azure Active Directory, отличную от той, которую вы хотите использовать для Виртуального рабочего стола Windows, необходимо выйти из системы или использовать вкладку браузера в режиме конфиденциального просмотра.

После входа в систему отобразится список ресурсов. Чтобы запустить ресурсы, выберите их, как при работе с обычным приложением, на вкладке **Все ресурсы**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании веб-клиента см. в статье [Начало работы с веб-клиентом](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
