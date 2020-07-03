---
title: Подключение веб-клиента виртуальных рабочих столов Windows — Azure
description: Как подключиться к виртуальному рабочему столу Windows с помощью веб-клиента.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c264006d51f8f0f9ee2a56e994a0dc52d24bd7a8
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006038"
---
# <a name="connect-with-the-web-client"></a>Подключение к веб-клиенту

>[!IMPORTANT]
>Это содержимое относится к выпуску 2019, который не поддерживает Azure Resource Manager объекты виртуальных рабочих столов Windows. Если вы пытаетесь управлять Azure Resource Manager объектов виртуальных рабочих столов Windows, представленных в обновлении пружины 2020, см. [эту статью](../connect-web.md).

Веб-клиент позволяет получать доступ к ресурсам виртуальных рабочих столов Windows из веб-браузера без продолжительного процесса установки.

>[!NOTE]
>В настоящее время веб-клиент не поддерживает мобильные ОС.

## <a name="supported-operating-systems-and-browsers"></a>Поддерживаемые операционные системы и браузеры

Хотя любой браузер, поддерживающий HTML5, должен работать, корпорация Майкрософт официально поддерживает следующие операционные системы и браузеры.

| Браузер           | Поддерживаемая ОС                     | Примечания               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari;      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Версия 55 или более поздняя |
| Google Chrome     | Windows, macOS, Linux, ОС Chrome |                     |

## <a name="access-remote-resources-feed"></a>Доступ к веб-каналу удаленных ресурсов

В браузере перейдите к [веб-клиенту виртуальных рабочих столов Windows](https://rdweb.wvd.microsoft.com/webclient) и выполните вход с помощью учетной записи пользователя.

>[!NOTE]
>Если вы используете выпуск Windows Virtual Desktop пружины 2020, используйте ссылку на веб-клиент в [этой статье](../connect-web.md) для подключения к ресурсам.

>[!NOTE]
>Если вы уже вошли с учетной записью Azure Active Directory, отличной от той, которую вы хотите использовать для виртуальных рабочих столов Windows, необходимо выйти из системы или использовать частное окно браузера.

После входа в систему вы увидите список ресурсов. Чтобы запустить ресурсы, выберите их, как обычное приложение, на вкладке **все ресурсы** .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании веб-клиента см. в статье [Приступая к работе с веб-клиентом](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
