---
title: Подключение к Windows Virtual Desktop Windows 10 или 7 - Azure
description: Как подключиться к Windows Virtual Desktop с помощью клиента Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154344"
---
# <a name="connect-with-the-windows-desktop-client"></a>Подключение к клиенту Windows Desktop

> Применяется к: Windows 7, Windows 10 и Windows 10 IoT Enterprise

Вы можете получить доступ к ресурсам Windows Virtual Desktop на устройствах с Windows 7, Windows 10 и Windows 10 IoT Enterprise с помощью клиента Windows Desktop.

> [!IMPORTANT]
> Windows Virtual Desktop не поддерживает клиента RemoteApp и Desktop Connections (RADC) или клиента удаленного соединения рабочего стола (MSTSC).

> [!IMPORTANT]
> В настоящее время Windows Virtual Desktop не поддерживает клиент Remote Desktop из Магазина Windows. Поддержка этого клиента будет добавлена в будущем выпуске.

## <a name="install-the-windows-desktop-client"></a>Установка клиента Windows Desktop

Выберите клиент, который соответствует вашей версии Windows:

- [Windows 64-битная](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows (32-разрядная версия)](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows для ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Можно установить клиент для текущего пользователя, что не требует прав администратора. Кроме того, администратор может установить и настроить клиент, чтобы все пользователи устройства могли получить к нему доступ.

После установки клиент можно будет запустить из меню "Пуск", выполнив поиск фразы **Удаленный рабочий стол**.

## <a name="subscribe-to-a-feed"></a>Подписка на веб-канал

Получите список управляемых ресурсов, доступных для вас, подписавшись на канал, предоставленный вашим админом. Подписка делает ресурсы доступными на локальном компьютере.

Чтобы подписаться на канал:

1. Откройте клиент Windows Desktop.
2. Выберите **Подписку** на главной странице, чтобы подключиться к службе и получить ресурсы.
3. При появлении запроса войдите в систему со своей учетной записью.

После успешного вххостания вы увидите список ресурсов, к которые можно получить доступ.

Вы можете запустить ресурсы одним из двух методов.

- С главной страницы клиента, дважды нажмите ресурс, чтобы запустить его.
- Запуск ресурса, как вы обычно бы другие приложения из меню Пуск.
  - Вы также можете искать приложения в панели поиска.

После подписки на канал содержимое канала обновляется автоматически на регулярной основе. Ресурсы могут быть добавлены, изменены или удалены на основе изменений, внесенных администратором.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о том, как использовать клиент Windows Desktop, проверьте [Начать работу с клиентом Windows Desktop.](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)
