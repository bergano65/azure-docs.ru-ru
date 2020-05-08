---
title: Подключение веб-клиента виртуальных рабочих столов Windows — Azure
description: Как подключиться к виртуальному рабочему столу Windows с помощью веб-клиента.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70fd29944d31682de5a6a34f76fdf27e3ff7d3ff
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607849"
---
# <a name="connect-with-the-web-client"></a>Подключение к веб-клиенту

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows. Если вы используете виртуальный рабочий стол Windows в выпуске 2019 без Azure Resource Manager объектов, см. [эту статью](./virtual-desktop-fall-2019/connect-web-2019.md).
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

В браузере перейдите к [веб-клиенту виртуальных рабочих столов Windows](https://rdweb.wvd.microsoft.com/arm/webclient) и выполните вход с помощью учетной записи пользователя.

>[!NOTE]
>Если вы уже вошли с учетной записью Azure Active Directory, отличной от той, которую вы хотите использовать для виртуальных рабочих столов Windows, необходимо выйти из системы или использовать частное окно браузера.

После входа в систему вы увидите список ресурсов. Чтобы запустить ресурсы, выберите их, как обычное приложение, на вкладке **все ресурсы** .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании веб-клиента см. в статье [Приступая к работе с веб-клиентом](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
