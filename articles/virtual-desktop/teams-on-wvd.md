---
title: Microsoft Команды на Windows Виртуальный рабочий стол - Azure
description: Как использовать команды Майкрософт на виртуальном рабочем столе Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 702df1bfaf0701a40e514f8a47151f96917adf38
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811595"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Используйте команды Майкрософт на виртуальном рабочем столе Windows

> Применяется к: Windows 10 и Windows 10 IoT Enterprise

Виртуализированные среды представляют собой уникальный набор проблем для приложений для совместной работы, таких как Microsoft Teams, включая повышенную задержку, высокое использование процессора хоста и плохую общую производительность звука и видео. Чтобы узнать больше об использовании команд Майкрософт в средах VDI, ознакомьтесь [с группами виртуальной настольной инфраструктуры.](https://docs.microsoft.com/microsoftteams/teams-for-vdi)

## <a name="prerequisites"></a>Предварительные требования

Прежде чем использовать команды Майкрософт на виртуальном рабочем столе Windows, вам нужно сделать следующие вещи:

- Установка [клиента Windows Desktop](connect-windows-7-and-10.md) на устройстве Windows 10, отвечающее требованиям [оборудования](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Microsoft Teams.
- Подключитесь к Windows 10 Multi-сессия или Windows 10 Предприятие виртуальной машины (VM).
- [Подготовьте свою сеть](https://docs.microsoft.com/microsoftteams/prepare-network) для команд Майкрософт.

## <a name="use-unoptimized-microsoft-teams"></a>Использование неоптимизированных команд Майкрософт

Вы можете использовать неоптимизированные команды Microsoft в средах Windows Virtual Desktop, чтобы использовать функции полного чата и совместной работы команд Майкрософт, а также аудиовызовы. Качество звука в вызовах будет варьироваться в зависимости от конфигурации хоста, поскольку неоптимизированные вызовы используют больше вашего процессора.

### <a name="prepare-your-image-for-teams"></a>Подготовьте свой образ для команд

Чтобы включить установку Teams в машине, установите следующий ключ реестра на хосте:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 0x1
```

### <a name="install-microsoft-teams"></a>Установка команд Майкрософт

Вы можете развернуть настольное приложение Teams с помощью установки для одного компьютера. Для установки команд Майкрософт в среде виртуального рабочего стола Windows:

1. Скачать [пакет Teams MSI,](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) который соответствует вашей среде. Рекомендуем использовать 64-разрядный установщик на 64-битной операционной системе.
2. Запустите эту команду, чтобы установить MSI на хост VM.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Это позволит установить команды либо файлы программы или файлы программы (x86). При следующем входе в команду и запуске команды приложение запросит ваши учетные данные.

      > [!NOTE]
      > Пользователи и админы не могут отключить автоматический запуск для команд во время входиный в это время.

      Чтобы удалить MSI из хост VM, запустите эту команду:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Если вы установите команды с настройкой MSI ALLUSERS-1, автоматические обновления будут отключены. Мы рекомендуем вам не забудьте обновлять команды по крайней мере один раз в месяц.
