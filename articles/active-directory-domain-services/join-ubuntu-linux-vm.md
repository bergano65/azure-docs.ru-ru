---
title: Доменные службы Azure Active Directory. Присоединение виртуальной машины Ubuntu к управляемому домену | Документация Майкрософт
description: Присоединение виртуальной машины Ubuntu Linux к управляемому домену Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 80dbb4f3d0c8b993beab5f6344d6034d6c2b6895
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990581"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Присоединение виртуальной машины Ubuntu к управляемому домену в Azure
Из этой статьи вы узнаете, как присоединить виртуальную машину Ubuntu Linux к управляемому домену доменных служб Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Перед началом работы
Чтобы выполнить задачи, описанные в этой статье, вам потребуется следующее:  
1. Действующая **подписка Azure**.
2. **Каталог Azure AD** — синхронизированный с локальным каталогом или каталогом только для облака.
3. **Доменные службы Azure AD** должны быть включены для каталога Azure AD. Если это еще не сделано, выполните все задачи, описанные в [руководстве по началу работы](tutorial-create-instance.md).
4. Обязательно укажите IP-адреса управляемого домена в качестве DNS-серверов для виртуальной сети. Дополнительные сведения см. в статье об [изменении настроек DNS виртуальной сети Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).
5. Выполните шаги, необходимые для [синхронизации паролей с управляемым доменом доменных служб Azure AD](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Подготовка виртуальной машины Ubuntu Linux
Подготовьте виртуальную машину Ubuntu Linux в Azure любым из указанных ниже методов.
* [портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [Интерфейс командной строки Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Разверните виртуальную машину в **виртуальной сети, для которой включены доменные службы Azure AD**.
> * Выберите **другую подсеть** (не ту, для которой включены доменные службы Azure AD).
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Удаленное подключение к виртуальной машине Ubuntu Linux
Вы подготовили виртуальную машину Ubuntu Linux в Azure. Следующая задача — установить удаленное подключение к виртуальной машине, используя учетную запись локального администратора, созданную при подготовке виртуальной машины.

Следуйте инструкциям в статье [как войти в виртуальную машину под управлением Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Настройка файла hosts на виртуальной машине Linux
В окне терминала SSH откройте файл /etc/hosts для редактирования, чтобы изменить в нем IP-адрес и имя узла вашего компьютера.

```console
sudo vi /etc/hosts
```

Добавьте следующее значение в файл hosts:

```console
127.0.0.1 contoso-ubuntu.contoso.com contoso-ubuntu
```

Здесь "contoso.com" — это доменное DNS-имя управляемого домена. contoso-ubuntu — это имя узла виртуальной машины Ubuntu, присоединяемой к управляемому домену.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Установка требуемых пакетов на виртуальную машину Linux
После этого установите на виртуальную машину пакеты, необходимые для присоединения к домену. Выполните следующие действия:

1.  В терминале SSH введите команду ниже, чтобы загрузить списки пакетов из репозиториев. Эта команда обновляет списки пакетов для получения сведений о последних версиях пакетов и их зависимостях.

    ```console
    sudo apt-get update
    ```

2. Введите следующую команду, чтобы установить необходимые пакеты.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Во время установки Kerberos экран будет розовым. При установке пакета krb5-user поступает запрос на имя области (ВСЕ символы названия должны быть в ВЕРХНЕМ РЕГИСТРЕ). В процессе установки разделы [realm] и [domain_realm] сохраняются в файл /etc/krb5.conf.

    > [!TIP]
    > Если имя управляемого домена — contoso.com, введите CONTOSO.COM в качестве области. Не забывайте, что имя должно быть в ВЕРХНЕМ РЕГИСТРЕ.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Настройка параметров протокола NTP на виртуальной машине Linux
Дата и время на виртуальной машине Ubuntu должны синхронизироваться с управляемым доменом. Добавьте имя узла NTP из управляемого домена в файл /etc/ntp.conf.

```console
sudo vi /etc/ntp.conf
```

Добавьте следующее значение в файл ntp.conf и сохраните этот файл:

```console
server contoso.com
```

Здесь "contoso.com" — это доменное DNS-имя управляемого домена.

Теперь на виртуальной машине Ubuntu выполните синхронизацию даты и времени с NTP-сервером, а затем запустите службу NTP.

```console
sudo systemctl stop ntp
sudo ntpdate contoso.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Присоединение виртуальной машины Linux к управляемому домену
Теперь, когда все требуемые пакеты установлены на виртуальной машине Linux, мы готовы присоединить виртуальную машину к управляемому домену.

1. Выполните поиск управляемого домена доменных служб AAD. В окне терминала SSH введите следующую команду:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   > [!NOTE]
   > **Устранение неполадок**. Если команда *realm discover* не может найти управляемый домен, сделайте следующее:
   >   * Проверьте подключение между доменом и виртуальной машиной (с помощью команды ping).
   >   * Убедитесь, что виртуальная машина развернута в одной виртуальной сети с управляемым доменом.
   >   * Проверьте, обновлены ли параметры DNS-сервера для виртуальной сети — должны быть указаны контроллеры управляемого домена.

2. Инициализируйте Kerberos. В окне терминала SSH введите следующую команду:

    > [!TIP]
    > * Обязательно укажите пользователя, который принадлежит к группе "Администраторы AAD AD". При необходимости [добавьте учетную запись пользователя в группу в Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md) .
    > * Введите доменное имя заглавными буквами, иначе операция с использованием kinit завершится ошибкой.
    >

    ```console
    kinit bob@CONTOSO.COM
    ```

3. Присоедините компьютер к домену. В окне терминала SSH введите следующую команду:

    > [!TIP]
    > Используйте ту же учетную запись пользователя, которую вы указали на предыдущем шаге (kinit).
    >
    > Если виртуальной машине не удается присоединиться к домену, убедитесь, что группа безопасности сети виртуальной машины разрешает исходящий трафик Kerberos для TCP + UDP-порта 464 в подсети виртуальной сети для управляемого домена Azure AD DS.

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'bob@CONTOSO.COM' --install=/
    ```

Когда компьютер присоединится к управляемому домену, вы получите сообщение "Компьютер успешно зарегистрирован в realm" (Successfully enrolled machine in realm).


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Обновление конфигурации SSSD и перезапуск службы
1. В окне терминала SSH введите команду ниже. Откройте файл sssd.conf и внесите в него следующее изменение:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Закомментируйте строку **use_fully_qualified_names = True** и сохраните файл.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Перезапустите службу SSHD.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Настройка автоматического создания домашнего каталога
Чтобы включить автоматическое создание домашнего каталога после входа в систему пользователей, введите следующие команды в терминале вывода:

```console
sudo vi /etc/pam.d/common-session
```

В этом файле под строкой session optional pam_sss.so добавьте новую строку с указанными ниже данными и сохраните файл.

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Проверка присоединения к домену
Проверьте, присоединена ли виртуальная машина к управляемому домену. Подключитесь к виртуальной машине Ubuntu, присоединенной к домену, используя другое SSH-подключение. Используйте учетную запись пользователя домена и проверьте, правильно ли разрешится эта учетная запись.

1. В окне терминала SSH введите команду ниже, чтобы подключиться по протоколу SSH к виртуальной машине Ubuntu, присоединенной к домену. Используйте учетную запись домена, которая принадлежит к управляемому домену (в нашем примере — bob@CONTOSO.COM).
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-ubuntu.contoso.com
    ```

2. Чтобы проверить, правильно ли инициализирован корневой каталог, в окне терминала SSH введите следующую команду:
    
    ```console
    pwd
    ```

3. Чтобы проверить, правильно ли определено членство в группе, в окне терминала SSH введите следующую команду:
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Предоставление привилегий суперпользователя группе "Администраторы контроллера домена AAD"
Вы можете предоставить всем членам группы "Администраторы контроллера домена AAD" административные привилегии на виртуальной машине Ubuntu. Файл sudo хранится в файле /etc/sudoers. Члены групп AD, добавленных в этот файл, имеют право выполнять команду sudo.

1. Убедитесь, что в терминале SSH выполнен вход с правами суперпользователя. Для этого можно использовать учетную запись локального администратора, которую вы указали при создании виртуальной машины. Выполните следующую команду:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Добавьте следующую запись в файл /etc/sudoers и сохраните его:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Теперь вы можете войти в систему как член группы "Администраторы контроллера домена AAD" и иметь права администратора на виртуальной машине.


## <a name="troubleshooting-domain-join"></a>Устранение неполадок при присоединении к домену
См. статью, посвященную [устранению неполадок при присоединении к домену](join-windows-vm.md#troubleshoot-domain-join-issues).


## <a name="related-content"></a>См. также
* [Приступая к работе с доменными службами Azure AD](tutorial-create-instance.md)
* [Присоединение виртуальной машины Windows Server к управляемому домену](active-directory-ds-admin-guide-join-windows-vm.md)
* [Как войти в виртуальную машину под управлением Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
