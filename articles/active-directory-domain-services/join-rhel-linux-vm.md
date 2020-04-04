---
title: Присоединиться к RHEL VM к службам домена Azure AD Domain Services Документы Майкрософт
description: Узнайте, как настроить и присоединиться к виртуальной машине Red Hat Enterprise Linux к домену Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 81eec19cb4af3a6b668bbfc26105085b4eec2a19
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655148"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain (Присоединение виртуальной машины Red Hat Enterprise Linux к управляемому домену доменных служб Azure AD)

Чтобы позволить пользователям войти в виртуальные машины (Виртуальные технологии) в Azure, используя единый набор учетных данных, можно присоединиться к виртуальным всасданным службам функционального каталога Azure (AD DS), управляемым доменом. При присоединении к VM к домену Azure AD DS, учетные записи пользователей и учетные данные домена могут использоваться для ввоза и управления серверами. Членство в группе из домена Azure AD DS также применяется, чтобы позволить вам контролировать доступ к файлам или службам на VM.

В этой статье показано, как присоединиться к VM Red Hat Enterprise Linux (RHEL) к домену Azure AD DS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас нет подписки НаAz, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, по инструкциям из первого учебника [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Учетная запись пользователя, войдя часть домена Azure AD DS.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Создание и подключение к RHEL Linux VM

Если у вас есть существующий RHEL Linux VM в Azure, подключитесь к нему с помощью SSH, а затем перейдите на следующий шаг, чтобы [начать настройку VM.](#configure-the-hosts-file)

Если вам нужно создать RHEL Linux VM, или хотите создать тест VM для использования в этой статье, вы можете использовать один из следующих методов:

* [Портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

При создании VM обратите внимание на настройки виртуальной сети, чтобы убедиться, что VM может общаться с доменом Azure AD DS:

* Развертывание VM в ту же или заглянувую виртуальную сеть, в которой вы включили службы домена Azure AD.
* Развертывание VM в другую подсеть, чем экземпляр службы домена Azure AD.

После развертывания VM выполните последующие действия по подключению к VM с помощью SSH.

## <a name="configure-the-hosts-file"></a>Настройка файла hosts

Чтобы убедиться, что имя хоста VM правильно настроено для управляемого домена, отредактьте файл */etc/hosts* и установите имя хоста:

```console
sudo vi /etc/hosts
```

В файле *хостов* обновите адрес *локального хозяина.* Рассмотрим следующий пример:

* *aaddscontoso.com* — доменное имя DNS вашего домена Azure AD DS.
* *rhel* — это хост-имя вашего RHEL VM, к которому вы присоединяетесь к управляемому домену.

Обновление этих имен с вашими собственными значениями:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

При этом сохраняй и выходизьте файл *хоста,* `:wq` используя команду редактора.

## <a name="install-required-packages"></a>Установка необходимых пакетов

VM нужны дополнительные пакеты, чтобы присоединиться к VM в домене Azure AD DS. Для установки и настройки этих пакетов, обновить `yum`и установить домен-соединение инструментов с помощью. Есть некоторые различия между RHEL 7.x и RHEL 6.x, поэтому используйте соответствующие команды для дистро версии в остальных разделах этой статьи.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**РЭЛ 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Присоединиться к VM к управляемому домену

Теперь, когда необходимые пакеты установлены на VM, присоединяйтесь к VM к домену Azure AD DS. Опять же, используйте соответствующие шаги для вашей версии RHEL дистро.

### <a name="rhel-7"></a>RHEL 7

1. Используйте `realm discover` команду, чтобы открыть для себя домена Azure AD DS. Следующий пример обнаруживает, что царство *AADDSCONTOSO.COM*. Укажите свой собственный Azure AD DS управляемых доменное имя в ВСЕХ UPPERCASE:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Если `realm discover` команда не может найти свой домен СУ AD СAzура, просмотрите следующие шаги устранения неполадок:

    * Убедитесь, что домен довисот от VM. Попробуйте `ping aaddscontoso.com` увидеть, если положительный ответ возвращается.
    * Убедитесь, что VM развернут в той же виртуальной сети, в которой доступен домен Azure AD DS.
    * Подтвердите, что настройки DNS сервера для виртуальной сети были обновлены, чтобы указать на контроллеры домена в домене Azure AD DS.

1. Теперь инициализируем Кербероса с помощью `kinit` команды. Укажите пользователя, который является частью домена Azure AD DS. При необходимости [добавьте учетную запись пользователя в группу в Azure AD.](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)

    Опять же, доменное имя Azure AD DS, управляемое DS, должно быть введено в ALL UPPERCASE. В следующем примере названная `contosoadmin@aaddscontoso.com` учетная запись используется для инициализации Kerberos. Введите свою собственную учетную запись пользователя, которая является частью домена Azure AD DS:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Наконец, присоединитесь к машине к домену `realm join` Azure AD DS, управляемому с помощью команды. Используйте ту же учетную запись пользователя, которая является частью домена Azure AD DS, указанного в предыдущей `kinit` команде, `contosoadmin@AADDSCONTOSO.COM`например:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Требуется несколько минут, чтобы присоединиться к VM в домене Azure AD DS. Следующий пример вывода показывает, что VM успешно присоединился к домену Azure AD DS:

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>РЭЛ 6

1. Используйте `adcli info` команду, чтобы открыть для себя домена Azure AD DS. Следующий пример обнаруживает область *ADDDSCONTOSO.COM*. Укажите свой собственный Azure AD DS управляемых доменное имя в ВСЕХ UPPERCASE:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Если `adcli info` команда не может найти свой домен СУ AD СAzура, просмотрите следующие шаги устранения неполадок:

    * Убедитесь, что домен довисот от VM. Попробуйте `ping aaddscontoso.com` увидеть, если положительный ответ возвращается.
    * Убедитесь, что VM развернут в той же виртуальной сети, в которой доступен домен Azure AD DS.
    * Подтвердите, что настройки DNS сервера для виртуальной сети были обновлены, чтобы указать на контроллеры домена в домене Azure AD DS.

1. Во-первых, присоединиться `adcli join` к домену с помощью команды, эта команда также создаст ключ для проверки подлинности машины. Используйте учетную запись пользователя, которая является частью домена Azure AD DS.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Теперь `/ect/krb5.conf` назначайте `/etc/sssd/sssd.conf` и создавайте файлы для использования домена `aaddscontoso.com` Active Directory.
   Убедитесь, `AADDSCONTOSO.COM` что заменяется ваше собственное доменное имя:

    Откройте `/ect/krb5.conf` файл с редактором:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Обновление `krb5.conf` файла в соответствии со следующим примером:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Создайте `/etc/sssd/sssd.conf` файл:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Обновление `sssd.conf` файла в соответствии со следующим примером:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Убедитесь, что `/etc/sssd/sssd.conf` разрешения 600 и принадлежат корневому пользователю:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Используйте `authconfig` для инструктажа VM об интеграции AD Linux:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Запустите и включите службу sssd:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Если ваш VM не может успешно завершить процесс соединения домена, убедитесь, что группа сетевой безопасности VM позволяет исходящих Kerberos трафика на TCP и UDP порт 464 в виртуальную сеть подсети для вашего Azure AD DS управляемых домена.

Теперь проверьте, можно ли задавливать запрос информации AD пользователя с помощью`getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Разрешить проверку подлинности пароля для SSH

По умолчанию пользователи могут войти в VM только с помощью общедоступной аутентификации на основе ключей SSH. Проверка подлинности на основе паролей не удается. При присоединении к VM к домену Azure AD DS эти учетные записи домена должны использовать проверку подлинности на основе паролей. Обновление конфигурации SSH, чтобы позволить проверку подлинности на основе паролей следующим образом.

1. Откройте *файл sshd_conf* с редактором:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Обновление строки для *PasswordAuthentication* *да*:

    ```console
    PasswordAuthentication yes
    ```

    При этом сохраньте и выйдите из *sshd_conf* файла, `:wq` используя команду редактора.

1. Чтобы применить изменения и позволить пользователям войти в систему с помощью пароля, перезапустите сервис SSH для вашей версии RHEL distro:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **РЭЛ 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Предоставление привилегий суперпользователя группе "Администраторы контроллера домена AAD"

Чтобы предоставить членам *AAD DC Администраторы группы административных* привилегий на RHEL VM, вы добавляете запись *на / и т.д. / судосов*. После добавления члены *группы администраторов AAD DC* могут использовать `sudo` команду на RHEL VM.

1. Откройте файл *sudoers* для редактирования:

    ```console
    sudo visudo
    ```

1. Добавьте следующую запись в конец *файла /etc/sudoers.* Группа *администраторов AAD DC* содержит пробел в имени, поэтому включите символ побега backslash в имя группы. Добавьте свое собственное доменное имя, например *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    При этом сохраните и выйдите из редактора, `:wq` используя команду редактора.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Войти на VM с помощью учетной записи домена

Чтобы убедиться, что VM был успешно подключен к домену Azure AD DS, запустите новое соединение SSH с помощью учетной записи пользователя домена. Подтвердите, что домашний каталог был создан, и что членство в группе из домена применяется.

1. Создайте новое соединение SSH с консоли. Используйте учетную запись домена, которая `ssh -l` принадлежит управляемому домену, используя команду, `contosoadmin@aaddscontoso.com` например, а затем введите адрес вашего VM, *например, rhel.aaddscontoso.com*. Если вы используете Лазурную облачную оболочку, используйте общедоступный IP-адрес VM, а не внутреннее имя DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Когда вы успешно подключены к VM, убедитесь, что домашний каталог был правильно инициализирован:

    ```console
    pwd
    ```

    Вы должны быть в */домашнем* каталоге с собственным каталогом, который соответствует учетной записи пользователя.

1. Теперь убедитесь, что членство в группе решается правильно:

    ```console
    id
    ```

    Вы должны видеть членство в группе из домена Azure AD DS.

1. Если вы зарегистрировались в VM в качестве члена *группы администраторов AAD DC,* убедитесь, что вы можете правильно использовать `sudo` команду:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Дальнейшие действия

Если у вас возникли проблемы с подключением VM к домену Azure [Troubleshooting domain join issues](join-windows-vm.md#troubleshoot-domain-join-issues)AD DS или вовремя с помощью учетной записи домена, см.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
