---
title: Подготовка и настройка изображения мастера VHD - Azure
description: Как подготовить, настроить и загрузить изображение мастера рабочего стола Windows Virtual Desktop в Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127730"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Подготовка и настройка главного образа VHD

В этой статье рассказывается о том, как подготовить изображение виртуального жесткого диска (VHD) для загрузки в Azure, в том числе о том, как создавать виртуальные машины (Виртуальные машины) и устанавливать на них программное обеспечение. Эти инструкции предназначены для конфигурации Виртуального рабочего стола Windows, которую можно использовать в имеющихся процессах вашей организации.

## <a name="create-a-vm"></a>Создание виртуальной машины

Многосессионная сессия Windows 10 Enterprise доступна в галерее изображений Azure. Существует два варианта настройки этого изображения.

Первый вариант заключается в предоставлении виртуальной машины (VM) в Azure, следуя инструкциям в [создании VM из управляемого изображения,](../virtual-machines/windows/create-vm-generalized-managed.md)а затем перейти вперед к [подготовке и установке программного обеспечения.](set-up-customize-master-image.md#software-preparation-and-installation)

Второй вариант заключается в создании изображения локально, загрузив изображение, подготовка Hyper-V VM, и настроить его в соответствии с вашими потребностями, которые мы рассмотрим в следующем разделе.

### <a name="local-image-creation"></a>Локальное создание изображений

После загрузки изображения в локальное место, откройте **Hyper-V Manager** для создания VM с скопированным VHD. Следующие инструкции являются простой версией, но вы можете найти более подробные инструкции в [Создать виртуальную машину в Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Чтобы создать VM с скопированным VHD:

1. Откройте **новую виртуальную машину Мастера**.

2. На странице «Определить поколение» выберите **поколение 1**.

    ![Скриншот страницы «Поколение уточнения». Выбран опция "Поколение 1".](media/a41174fd41302a181e46385e1e701975.png)

3. Под типом контрольной точки, отключить контрольно-пропускные пункты, отображая флажок.

    ![Скриншот раздела Типа контрольного точек на странице Контрольно-пропускных пунктов.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Вы также можете запустить следующий cmdlet в PowerShell, чтобы отключить контрольно-пропускные пункты.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Фиксированный диск

При создании VM из существующего VHD он создает динамический диск по умолчанию. Он может быть изменен на фиксированный диск, выбрав **Edit Disk...,** как показано на следующем изображении. Для получения более подробных инструкций смотрите [подготовьте Windows VHD или VHDX для загрузки в Azure.](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

![Скриншот опции Edit Disk.](media/35772414b5a0f81f06f54065561d1414.png)

Вы также можете запустить следующий смдлет PowerShell, чтобы изменить диск на фиксированный диск.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Подготовка и установка программного обеспечения

В этом разделе рассказывается о том, как подготовить и установить FSLogix и Windows Defender, а также некоторые основные параметры конфигурации для приложений и реестра изображений. 

Если вы устанавливаете Office 365 ProPlus и OneDrive на VM, перейдите в [Install Office на изображение мастера VHD](install-office-on-wvd-master-image.md) и следуйте инструкциям, чтобы установить приложения. После того как вы закончите, вернитесь к этой статье.

Если пользователям необходим доступ к определенным приложениям LOB, мы рекомендуем вам установить их после выполнения инструкций этого раздела.

### <a name="set-up-user-profile-container-fslogix"></a>Настройка контейнера профиля пользователя (FSLogix)

Чтобы включить контейнер FSLogix в изображение, следуйте инструкциям в [создании контейнера профиля для пула хоста, используя общую копилку файла.](create-host-pools-user-profile.md#configure-the-fslogix-profile-container) Вы можете проверить функциональность контейнера FSLogix с [помощью этого быстрого запуска.](/fslogix/configure-cloud-cache-tutorial/)

### <a name="configure-windows-defender"></a>Настройка Windows Defender

Если Windows Defender настроена в VM, убедитесь, что он настроен, чтобы не сканировать все содержимое файлов VHD и VHDX во время вложения.

Эта конфигурация удаляет только сканирование файлов VHD и VHDX во время вложения, но не повлияет на сканирование в режиме реального времени.

Для получения более подробных инструкций о том, как настроить Windows Defender на Windows Server, [см.](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)

Чтобы узнать больше о том, как настроить Windows Defender, чтобы исключить некоторые файлы из сканирования, [см. Настройте и проверить исключения на основе расширения файла и расположения папок.](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)

### <a name="disable-automatic-updates"></a>Отключить автоматические обновления

Чтобы отключить автоматические обновления через локальные групповые политики:

1. Открыть **редактор политики\\локальной группы Административные шаблоны Windows Компоненты\\\\Windows Обновление**.
2. Правый щелчок **Наконкажеслите автоматическое обновление** и установите его **на отключение.**

Вы также можете запустить следующую команду в запросе команды, чтобы отключить автоматические обновления.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Укажите макет старта для ПК с Windows 10 (необязательно)

Запустите эту команду, чтобы указать макет start для ПК с Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Настройка перенаправления часового пояса

Перенаправление часового пояса может быть осуществлено на уровне групповой политики, так как все ввожжа в пуле хостов являются частью одной и той же группы безопасности.

Перенаправлять часовые пояса:

1. На сервере Active Directory откройте **консоль управления политикой группы.**
2. Расширьте объекты контора и групповой политики.
3. Нажмите правой кнопкой мыши **на объекте групповой политики,** который вы создали для настроек групповой политики, и выберите **Edit.**
4. В **редакторе отдела управления политиками группы**перейдите к **политике компьютерной** > **конфигурации Административные** > **шаблоны** > **Windows Компоненты** > **Удаленного настольного устройства** > **удаленного рабочего стола** > и**перенаправления ресурсов.**
5. Включить настройки **перенаправления часового пояса Разрешить.**

Вы также можете запустить эту команду на главном изображении, чтобы перенаправить часовые пояса:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Неиссякаемое чувство хранения

Для хостов сеанса виртуального рабочего стола Windows, использующих Windows 10 Enterprise или Windows 10 Enterprise multi-session, мы рекомендуем отключить чувство хранения. Вы можете отключить чувство хранения в меню настроек под **хранением,** как показано на следующем скриншоте:

![Скриншот меню хранения под настройками. Опция "Хранение смысла" отключена.](media/storagesense.png)

Вы также можете изменить настройку с реестром, запустив следующую команду:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Включите дополнительную языковую поддержку

В этой статье не говорится о том, как настроить языковую и региональную поддержку. Дополнительные сведения см. в следующих статьях:

- [Добавление языков к изображениям Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Особенности по запросу](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Особенности языка и региона по требованию (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Другие приложения и конфигурация реестра

Этот раздел охватывает конфигурацию приложений и операционной системы. Вся конфигурация в этом разделе осуществляется через записи реестра, которые могут быть выполнены с помощью командной строки и regedit инструментов.

>[!NOTE]
>Вы можете внедрить лучшие практики в конфигурации с помощью объектов групповой политики (ГПО) или импорта реестра. Администратор может выбрать любой вариант в зависимости от требований организации.

Для сбора данных телеметрии концентратора обратной связи на многосессии Windows 10 Enterprise запустите эту команду:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Выполнить следующую команду, чтобы исправить сбои Watson:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Введите следующие команды в редактор реестра, чтобы исправить поддержку разрешения 5k. Вы должны запустить команды, прежде чем вы сможете включить рядом стек.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Подготовка изображения к загрузке в Azure

После завершения конфигурации и установки всех приложений следуйте инструкциям в [подготовке Windows VHD или VHDX для загрузки](../virtual-machines/windows/prepare-for-upload-vhd-image.md) в Azure для подготовки изображения.

После подготовки изображения для загрузки, убедитесь, что VM остается в выключенном или deallocated состоянии.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Загрузка основного изображения в учетную запись хранения в Azure

Этот раздел применяется только тогда, когда основное изображение было создано локально.

Следующие инструкции расскажут вам, как загрузить основное изображение в учетную запись хранения Azure. Если у вас еще нет учетной записи хранения Azure, следуйте инструкциям в [этой статье,](/azure/javascript/tutorial-vscode-static-website-node-03) чтобы создать ее.

1. Преобразуйте изображение VM (VHD) в Исправлено, если вы еще не сделали этого. Если вы не преобразуете изображение в Исправленное, вы не можете успешно создать изображение.

2. Загрузите VHD в контейнер с каплей в учетной записи для хранения данных. Вы можете быстро загрузить с [помощью инструмента Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/) Чтобы узнать больше об инструменте Storage Explorer, смотрите [эту статью](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Скриншот окна поиска инструмента поиска Microsoft Azure Storage Explorer. Выбрана флажок "Upload .vhd или vhdx как капли страницы (рекомендуемые)" флажок.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Далее перейдите на портал Azure в браузере и ищите "Изображения". Поиск должен привести вас к странице **изображения Create,** как показано на следующем скриншоте:

    ![Скриншот страницы изображения Create портала Azure, заполненной примерными значениями для изображения.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. После создания изображения следует увидеть уведомление, аналогичное следующему скриншоту:

    ![Скриншот уведомления об «успешно созданном изображении».](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть изображение, вы можете создавать или обновлять пулы хостов. Чтобы узнать больше о том, как создавать и обновлять пулы хостов, смотрите следующие статьи:

- [Создание пула узлов с помощью шаблона Azure Resource Manager](create-host-pools-arm-template.md)
- [Учебник: Создание пула хоста с Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Создание пула узлов с помощью PowerShell](create-host-pools-powershell.md)
- [Создание контейнера профиля для пула узлов с помощью общей папки](create-host-pools-user-profile.md)
- [Настройка метода балансировки нагрузки Виртуального рабочего стола Windows](configure-host-pool-load-balancing.md)
