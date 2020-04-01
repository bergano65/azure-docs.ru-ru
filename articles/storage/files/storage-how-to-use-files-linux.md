---
title: Использование файлов Azure в Linux | Документы Майкрософт
description: Узнайте, как подключить файловый ресурс Azure через протокол SMB в Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72264755d5f0379f0ffb07852f48885126a36898
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411604"
---
# <a name="use-azure-files-with-linux"></a>Использование Файлов Azure в Linux
[Файлы Azure](storage-files-introduction.md) — это простая в использовании облачная файловая система от Майкрософт. Файловые ресурсы Azure можно подключить в дистрибутивах Linux с помощью [SMB-клиента в ядре](https://wiki.samba.org/index.php/LinuxCIFS). В этой статье описаны два способа подключения файлового ресурса Azure: по запросу с помощью команды `mount` и при загрузке путем создания записи в `/etc/fstab`.

Рекомендуемый способ установки доли файлов Azure в Linux — это использование SMB 3.0. По умолчанию Azure Files требует шифрования в пути, который поддерживается только SMB 3.0. Azure Files также поддерживает SMB 2.1, который не поддерживает шифрование в пути, но вы не можете монтировать акции файлов Azure с SMB 2.1 из другого региона Azure или в помещениях по соображениям безопасности. Если ваше приложение специально требует SMB 2.1, нет никаких оснований использовать его, поскольку наиболее популярны, недавно выпустила Дистрибутивы Linux поддержки SMB 3.0:  

| | SMB 2.1 <br>(подключение к виртуальным машинам в одном регионе) | SMB 3.0 <br>(подключение из локальной среды и между регионами) |
| --- | :---: | :---: |
| Ubuntu | 14.04 или более поздней версии | 16.04 или выше |
| Red Hat Enterprise Linux (RHEL) | 7 или выше | 7.5 или выше |
| CentOS | 7 или выше |  7.5 или выше |
| Debian | 8 или выше | 10+ |
| openSUSE | 13.2 или выше | 42.3 или выше |
| SUSE Linux Enterprise Server | 12+ | 12 SP3 или выше |

Если вы используете дистрибутив Linux, не указанный в приведенной выше таблице, вы можете проверить, поддерживает ли дистрибутив Linux SMB 3.0 с помощью шифрования, проверяя версию ядра Linux. SMB 3.0 с шифрованием был добавлен в версию ядра Linux 4.11. Команда `uname` вернет версию ядра Linux в использовании:

```bash
uname -r
```

## <a name="prerequisites"></a>Предварительные требования
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Убедитесь, что пакет cifs-utils установлен.**  
    Пакет cifs-utils можно установить с помощью диспетчера пакетов в дистрибутиве Linux по своему усмотрению. 

    В дистрибутивах **Ubuntu** и **на основе Debian** используйте диспетчер пакетов `apt`:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    На **Fedora**, **Red Hat Enterprise Linux 8 "** и **CentOS 8**, используйте менеджер пакетов: `dnf`

    ```bash
    sudo dnf install cifs-utils
    ```

    На старых версиях **Red Hat Enterprise** `yum` Linux и **CentOS**используйте менеджер пакетов:

    ```bash
    sudo yum install cifs-utils 
    ```

    В **openSUSE** используйте диспетчер пакетов `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    В других дистрибутивах используйте соответствующий диспетчер пакетов или выполните [компиляцию из источника](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Последняя версия интерфейса командной строки Azure (CLI).** Для получения дополнительной информации о том, как установить ClI Azure, [см.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Если вы предпочитаете использовать модуль Azure PowerShell в PowerShell 6, вы можете, однако приведенные ниже инструкции представлены для Azure CLI.

* **Откройте порт 445**. Взаимодействие SMB выполняется через TCP-порт 445. Проверьте, чтобы брандмауэр не блокировал TCP-порты 445 с клиентского компьютера.  Замените **<>группы ресурсов** и<>**вашей учетной записи**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Если соединение было успешным, вы должны увидеть что-то похожее на следующий вывод:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Если вы не можете открыть порт 445 в корпоративной сети или заблокированы интернет-пользователем, вы можете использовать VPN-соединение или ExpressRoute для работы вокруг порта 445. Для получения дополнительной [информации](storage-files-networking-overview.md)см.

## <a name="mounting-azure-file-share"></a>Установка общего файла Azure
Чтобы использовать общий файл Azure с дистрибутивом Linux, необходимо создать каталог, чтобы служить точкой крепления для общего файла Azure. Точка крепления может быть создана в любом месте вашей системы Linux, но это общая конвенция, чтобы создать это под /mnt. После точки крепления команда `mount` использует доступ к файлу Azure.

При желании можно установить один и тот же файл Azure на несколько точек крепления.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Подключение файлового ресурса Azure по запросу с помощью `mount`
1. **Создайте папку для точки крепления:** Заменить, `<your-resource-group>` `<your-storage-account>`и `<your-file-share>` с соответствующей информацией для вашей среды:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Используйте команду крепления для установки общего файла Azure.** В приведенном ниже примере локальные разрешения файлов Linux и папок по умолчанию 0755, что означает чтение, запись и выполнение для владельца (на основе владельца файла/каталога Linux), читать и выполнять для пользователей в группе владельцев, а также читать и выполнять для других в системе. Вы можете `uid` использовать `gid` параметры крепления для установки идентификатора пользователя и идентификатора группы для крепления. Вы также `dir_mode` можете `file_mode` использовать и устанавливать пользовательские разрешения по желанию. Для получения дополнительной информации о [UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) том, как установить разрешения, см. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Вышеупомянутая команда крепления крепления с SMB 3.0. Если дистрибутив Linux не поддерживает SMB 3.0 с помощью шифрования или поддерживает только SMB 2.1, вы можете смонтировать только из Azure VM в том же регионе, что и учетная запись хранилища. Чтобы установить свою долю файла Azure в дистрибутиве Linux, который не поддерживает SMB 3.0 с помощью шифрования, необходимо [отключить шифрование при переходе к учетной записи хранилища.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Завершив работу с файловым ресурсом Azure, вы можете отключить его, выполнив команду `sudo umount $mntPath`.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Создание постоянной точки подключения для файлового ресурса Azure с помощью `/etc/fstab`
1. **Создайте папку для точки крепления:** папка для точки крепления может быть создана в любом месте файловой системы, но это обычная конвенция, чтобы создать это под /mnt. Например, следующая команда создает новый `<your-resource-group>` `<your-storage-account>`каталог, `<your-file-share>` заменяет и сопожат с соответствующей информацией для среды:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Создайте файл учетных данных, в котором будет храниться имя пользователя (имя учетной записи хранения) и пароль (ключ учетной записи хранения) для имени файлового ресурса.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Измените разрешения для файла учетных данных таким образом, чтобы только пользователь с правами root мог читать или изменять файл пароля.** Поскольку ключ учетной записи хранения является паролем управляющего администратора учетной записи хранения, необходимо установить разрешения для файла, чтобы только у пользователя с правами root был доступ к файлу, а пользователи с более низким статусом прав не могли получить ключ учетной записи хранения.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Используйте следующую команду, чтобы `/etc/fstab`присвоить следующую строку: **В приведенном ниже примере локальный файл Linux и разрешения папки по умолчанию 0755, что означает чтение, запись и выполнение для владельца (на основе файла/каталога владельца Linux), читать и выполнять для пользователей в группе владельцев, а также читать и выполнять для других в системе. Вы можете `uid` использовать `gid` параметры крепления для установки идентификатора пользователя и идентификатора группы для крепления. Вы также `dir_mode` можете `file_mode` использовать и устанавливать пользовательские разрешения по желанию. Для получения дополнительной информации о [UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) том, как установить разрешения, см.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > Вышеупомянутая команда крепления крепления с SMB 3.0. Если дистрибутив Linux не поддерживает SMB 3.0 с помощью шифрования или поддерживает только SMB 2.1, вы можете смонтировать только из Azure VM в том же регионе, что и учетная запись хранилища. Чтобы установить свою долю файла Azure в дистрибутиве Linux, который не поддерживает SMB 3.0 с помощью шифрования, необходимо [отключить шифрование при переходе к учетной записи хранилища.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Использование автофов для автоматического монтажа доли файла Azure(ы)

1. **Убедитесь, что пакет autofs установлен.**  

    Пакет autofs может быть установлен с помощью менеджера пакетов на дистрибутиве Linux по вашему выбору. 

    В дистрибутивах **Ubuntu** и **на основе Debian** используйте диспетчер пакетов `apt`:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    На **Fedora**, **Red Hat Enterprise Linux 8 "** и **CentOS 8**, используйте менеджер пакетов: `dnf`
    ```bash
    sudo dnf install autofs
    ```
    На старых версиях **Red Hat Enterprise** `yum` Linux и **CentOS**используйте менеджер пакетов:
    ```bash
    sudo yum install autofs 
    ```
    В **openSUSE** используйте диспетчер пакетов `zypper`:
    ```bash
    sudo zypper install autofs
    ```
2. **Создайте точку крепления для доли (ы)**:
   ```bash
    sudo mkdir /fileshares
    ```
3. **Крит новый пользовательский файл конфигурации автофсов**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Добавьте следующие записи в /etc/auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Добавить следующую запись в /etc/auto.master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Перезагрузка автофсов**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Доступ к папке, предназначенной для акции**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Обеспечение безопасности Linux.
Для установки доли файлов Azure в Linux должен быть доступен порт 445. Многие организации блокируют порт 445 из-за угроз безопасности, присущих SMB 1. SMB 1, также известный как CIFS (Общая система файлов Интернета), является устаревшим протоколом файловой системы, включенным во многие дистрибутивы Linux. SMB 1 — это устаревший, неэффективный и, самое главное, небезопасный протокол. Хорошей новостью является то, что Azure Files не поддерживает SMB 1, и, начиная с версии ядра Linux 4.18, Linux позволяет отключить SMB 1. Мы всегда [настоятельно рекомендуем](https://aka.ms/stopusingsmb1) отключить SMB 1 на ваших клиентов Linux перед использованием SMB файловых акций в производстве.

Начиная с ядра Linux 4.18, модуль ядра SMB, называемый `cifs` по устаревшим причинам, предоставляет новый параметр модуля (часто именуемый *пармом* различными внешними документами), называемый `disable_legacy_dialects`. Несмотря на то, что ядра Linux были введены в Linux 4.18, некоторые поставщики вернули это изменение на старые ядра, которые они поддерживают. Для удобства в следующей таблице подробно описывается доступность этого параметра модуля на общих дистрибутивах Linux.

| Distribution | Может отключить SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Нет |
| Ubuntu 18.04 | Да |
| Увунту 19,04" | Да |
| Дебиан 8-9 | Нет |
| Дебиан 10 | Да |
| Федора 29 " | Да |
| CentOS 7 | Нет | 
| CentOS 8 " | Да |
| Red Hat Enterprise Linux 6.x-7.x | Нет |
| Red Hat Предприятие Linux 8 " | Да |
| openSUSE Прыжок 15.0 | Нет |
| openSUSE Прыжок 15,1 " | Да |
| openSUSE Поле для мыли | Да |
| SUSE Linux Enterprise 11.x-12.x | Нет |
| SUSE Linux Предприятие 15 | Нет |
| SUSE Linux Предприятие 15.1 | Нет |

Вы можете проверить, поддерживает ли `disable_legacy_dialects` дистрибутив Linux параметр модуля с помощью следующей команды.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Эта команда должна вывести следующее сообщение:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Прежде чем отключить SMB 1, необходимо проверить, чтобы убедиться, что модуль SMB в настоящее время не загружается в вашей системе (это происходит автоматически, если вы установили долю SMB). Это можно сделать со следующей командой, которая ничего не должна выжать, если SMB не загружается:

```bash
lsmod | grep cifs
```

Чтобы выгрузить модуль, сначала отразите `umount` все доли SMB (используя команду, описанную выше). Вы можете определить все установленные доли SMB в вашей системе со следующей командой:

```bash
mount | grep cifs
```

После того как вы расмонтированы все sMB файл акций, это безопасно для разгрузки модуля. Это можно сделать с помощью команды `modprobe` .

```bash
sudo modprobe -r cifs
```

Вы можете вручную загрузить модуль с помощью `modprobe` SMB 1, выгруженного с помощью команды:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Наконец, вы можете проверить модуль SMB был загружен с параметром, `/sys/module/cifs/parameters`глядя на загруженные параметры в:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Чтобы постоянно отскакивать SMB 1 в дистрибутивах на основе Ubuntu и Debian, необходимо создать `/etc/modprobe.d/local.conf` новый файл (если у вас еще нет пользовательских опций для других модулей), вызванный с настройкой. Вы можете сделать это со следующей командой:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Вы можете проверить, что это сработало, загрузив модуль SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Отзывы
Пользователи Linux, нам очень интересно ваше мнение!

Файлы Azure для группы пользователей Linux включают форум, на котором можно поделиться своим мнением и опытом адаптации хранилища файлов в Linux. Чтобы вступить в группу пользователей, отправьте электронное письмо в группу [Пользователи файлов Azure в Linux](mailto:azurefiles@microsoft.com).

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о службе файлов Azure см. по следующим ссылкам.

* [Планирование развертывания службы файлов Azure](storage-files-planning.md)
* [Вопросы и ответы](../storage-files-faq.md)
* [Устранение неполадок](storage-troubleshoot-linux-file-connection-problems.md)
