---
title: Устранение неполадок с обновлением поставщика службы Microsoft Azure Site Recovery | Документация Майкрософт
description: Общие сведения
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: 9423fc844e766129ad81a8a286cb5bbdc722e2ca
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989327"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Устранение неполадок с обновлением поставщика службы Microsoft Azure Site Recovery

Эта статья поможет устранить неполадки, которые могут возникнуть во время обновления поставщика службы Microsoft Azure Site Recovery.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Сбой обновления с сообщением о том, что последний поставщик службы Azure Site Recovery уже установлен

Во время обновления поставщика службы Microsoft Azure Site Recovery (DRA) происходит сбой обновления средства унифицированной установки и отображается такое сообщение об ошибке:

Upgrade is not supported as a higher version of the software is already installed. (Обновление не поддерживается, так как уже установлена более поздняя версия программного обеспечения.)

Чтобы обновить поставщика службы, выполните следующие действия:

1. Скачайте средство унифицированной установки Microsoft Azure Site Recovery:
   1. В разделе "Ссылки на поддерживаемые накопительные пакеты обновления" статьи [Обновления службы в Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) выберите поставщика, который вы обновляете.
   2. На открывшейся странице перейдите в раздел **Update information** (Сведения об обновлении) и скачайте накопительный пакет обновлений для средства унифицированной установки Microsoft Azure Site Recovery.

2. Откройте командную строку и перейдите к папке, куда вы скачали пакет. Извлеките файлы средства установки из скачанного пакета с помощью такой команды: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;путь к папке для извлеченных файлов&gt;.
    
    Пример команды:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. В командной строке перейдите в папку с извлеченными файлами и запустите такие команды для установки:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Вернитесь в папку со скачанным пакетом и запустите команду MicrosoftAzureSiteRecoveryUnifiedSetup.exe, чтобы завершить обновление. 

## <a name="upgrade-failure-due-to-the-thirdparty-folder-being-renamed"></a>Сбой обновления из-за того, что переименована папка thirdparty

Для успешного обновления нельзя менять имя папки thirdparty.

Устранение проблемы:

2. Запустите редактор реестра (regedit.exe) и откройте ветвь HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
3. Проверьте значение ключа `Build_Version`. Если установлено значение последней версии, измените версию на более старую. Например, если последняя версия — 9.22.\* и ключ `Build_Version` имеет это значение, измените версию на 9.21.\*.
4. Скачайте последнюю версию средства унифицированной установки Microsoft Azure Site Recovery:
   1. В разделе "Ссылки на поддерживаемые накопительные пакеты обновления" статьи [Обновления службы в Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) выберите поставщика, который вы обновляете.
   2. На открывшейся странице перейдите в раздел **Update information** (Сведения об обновлении) и скачайте накопительный пакет обновлений для средства унифицированной установки Microsoft Azure Site Recovery.
5. Откройте командную строку, перейдите в папку со скачанным пакетом и извлеките файлы средства установки из скачанного пакета с помощью такой команды: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;путь к папке для извлеченных файлов&gt;.

    Пример команды:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

4. В командной строке перейдите в папку с извлеченными файлами и запустите такие команды для установки:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

5. Для отслеживания процесса установки используйте диспетчер задач. Когда процесс CX_THIRDPARTY_SETUP.EXE больше не будет отображаться в диспетчере задач, перейдите к следующему этапу.
6. Убедитесь, что путь C:\thirdparty существует и папка содержит библиотеки RRD.
1. Вернитесь в папку со скачанным пакетом и запустите команду MicrosoftAzureSiteRecoveryUnifiedSetup.exe, чтобы завершить обновление. 