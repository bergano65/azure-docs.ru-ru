---
layout: LandingPage
description: Сведения об устранении неполадок с развертыванием виртуальных машин.
title: Документация по устранению неполадок с виртуальными машинами Azure | Документация Майкрософт
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: d7ceb3acb1d2e3d174f3b665ec6210d3ddac9970
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059166"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Устранение неполадок с виртуальными машинами Azure

## <a name="tools-for-troubleshooting"></a>Средства для устранения неполадок

- [Последовательная консоль](serial-console-windows.md)
- [Диагностика загрузки](boot-diagnostics.md)
- [Виртуальная машина Windows. Подключение диска ОС к другой виртуальной машине для устранения неполадок](troubleshoot-recovery-disks-portal-windows.md)
- [Виртуальная машина Linux. Подключение диска ОС к другой виртуальной машине для устранения неполадок](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Не удается подключиться к виртуальной машине

### <a name="windows"></a>Windows

**Распространенное решение**

- [Сброс RDP](reset-rdp.md)
- [Устранение неполадок с RDP](troubleshoot-rdp-connection.md)
- [Подробные сведения об устранении неполадок с RDP](detailed-troubleshoot-rdp.md)

**Ошибки RDP**

- [Сервер лицензирования отсутствует](troubleshoot-rdp-no-license-server.md)
- [Внутренние ошибки ](Troubleshoot-rdp-internal-error.md)
- [аутентификации](troubleshoot-authentication-error-rdp-vm.md)
- [Устранение определенных ошибок](troubleshoot-specific-rdp-errors.md)

**Ошибки загрузки виртуальной машины**

* [Ошибки загрузки BitLocker на виртуальной машине Azure](troubleshoot-bitlocker-boot-error.md) 
* [Отображение в Windows уведомления "Проверка файловой системы" при загрузке виртуальной машины Azure](troubleshoot-check-disk-boot-error.md)
* [Отображение ошибки "синий экран" при загрузке виртуальной машины Azure под управлением Windows](troubleshoot-common-blue-screen-error.md)
* [При запуске виртуальная машина зависла на сообщении "Подготовка Windows. Не выключайте компьютер" в Azure](troubleshoot-vm-boot-configure-update.md)
* [Windows отображает ошибку "Сбой критически важной службы" на синем экране при загрузке виртуальной машины Azure](troubleshoot-critical-service-failed-boot-error.md)
* [Цикл перезагрузки Windows на виртуальной машине Azure](troubleshoot-reboot-loop.md)
* [Зависание виртуальной машины при запуске во время обновления Windows](troubleshoot-stuck-updating-boot-error.md)
* [Виртуальная машина загружается в защищенном режиме](troubleshoot-rdp-safe-mode.md)

**другие.**
- [Сброс пароля виртуальной машины Windows в автономном режиме](reset-local-password-without-agent.md)
- [Сброс сетевого адаптера после применения неправильной настройки](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Устранение неполадок с SSH](troubleshoot-ssh-connection.md)
- [Подробные сведения об устранении неполадок с SSH](detailed-troubleshoot-ssh-connection.md)
- [Распространенные сообщения об ошибках](error-messages.md)
- [Сброс пароля виртуальной машины Linux в автономном режиме](reset-password.md)

## <a name="vm-deployment-issues"></a>Проблемы с развертыванием виртуальных машин

- [Ошибки выделения ресурсов](allocation-failure.md)
- Повторное развертывание виртуальной машины
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Устранение неполадок развертываний
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Изменение имен устройств](troubleshoot-device-names-problems.md)
- [Установка агента виртуальной машины Windows в автономном режиме](install-vm-agent-offline.md)
- [Перезапуск или изменение размера виртуальной машины](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Проблемы с производительностью виртуальных машин
- [Проблемы с производительностью виртуальных машин](performance-diagnostics.md)
- Windows
    - [Как использовать PerfInsights](how-to-use-perfinsights.md)
    - [Расширение для диагностики производительности](performance-diagnostics-vm-extension.md)
- Linux
    - [Как использовать PerfInsights](how-to-use-perfinsights-linux.md)