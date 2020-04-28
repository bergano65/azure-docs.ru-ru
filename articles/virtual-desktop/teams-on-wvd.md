---
title: Microsoft Teams на виртуальных рабочих столах Windows — Azure
description: Как использовать Microsoft Teams на виртуальных рабочих столах Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187534"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Использование Microsoft Teams в Windows виртуальный рабочий стол

> Область применения: Windows 10 и Windows 10 IoT Корпоративная

Виртуализованные среды представляют собой уникальный набор проблем для приложений для совместной работы, таких как Microsoft Teams, включая повышенную задержку, высокую загрузку ЦП узла и низкую общую производительность звука и видео. Дополнительные сведения об использовании Microsoft Teams в средах VDI см. в статье [группы для виртуализованной инфраструктуры настольных систем](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем вы сможете использовать Microsoft Teams на виртуальном рабочем столе Windows, вам потребуется выполнить следующие действия:

- Установите [клиент Windows Desktop](connect-windows-7-and-10.md) на устройстве Windows 10, удовлетворяющем [требованиям к оборудованию](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Microsoft Teams.
- Подключитесь к виртуальной машине Windows 10 с несколькими сеансами или Windows 10 Корпоративная (ВМ).
- [Подготовьте свою сеть](https://docs.microsoft.com/microsoftteams/prepare-network) для Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Использование неоптимизированных Microsoft Teams

Вы можете использовать Microsoft Teams в средах виртуальных рабочих столов Windows, чтобы воспользоваться функциями общения и совместной работы в Microsoft Teams. Виртуальный рабочий стол Windows не поддерживает команды на оптимизации аудио-и видеофайлов VDI (AV). Вызовы и собрания не поддерживаются. Если политики вашей организации позволяют, вы по-прежнему можете звонить и присоединяться к собраниям с помощью аудио, но неоптимизированное качество звука в вызовах будет зависеть от конфигурации узла и может быть ненадежной. Дополнительные сведения см. в статье [о производительности в VDI](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations).

### <a name="prepare-your-image-for-teams"></a>Подготовка образа для команд

Чтобы включить установку на компьютере группы, установите следующий раздел реестра на узле:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Установка Microsoft Teams

Вы можете развернуть классическое приложение Teams с помощью установки на компьютере. Чтобы установить Microsoft Teams в среде виртуальных рабочих столов Windows, выполните следующие действия.

1. Скачайте [пакет MSI для команд](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) , соответствующий вашей среде. Рекомендуется использовать 64-разрядный установщик в 64-разрядной операционной системе.
2. Выполните эту команду, чтобы установить MSI на виртуальную машину узла.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      При этом команды будут устанавливаться как в программных, так и в программных файлах (x86). При следующем входе в систему и запуске команд приложение запросит ваши учетные данные.

      > [!NOTE]
      > Пользователи и администраторы не могут отключить автоматический запуск для команд во время входа в систему.

      Чтобы удалить MSI с виртуальной машины узла, выполните следующую команду:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Если вы устанавливаете команды с параметром MSI АЛЛУСЕР = 1, автоматическое обновление будет отключено. Рекомендуется обновлять группы по крайней мере раз в месяц. Дополнительные сведения о развертывании классических приложений для рабочих групп см. в статье [Развертывание рабочего стола рабочих групп на виртуальной машине](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Настройка свойств протокол удаленного рабочего стола пула узлов
Настройка свойств протокол удаленного рабочего стола пула узлов (RDP), таких как использование нескольких мониторов, Включение перенаправления микрофона и звука, позволяет обеспечить оптимальную работу пользователей в зависимости от их потребностей. Свойства RDP можно настроить в виртуальном рабочем столе Windows с помощью параметра **-кустомрдппроперти** в командлете **Set-рдшостпул** .
Полный список поддерживаемых свойств и их значений по умолчанию см. в разделе [Поддерживаемые параметры RDP-файла](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .
