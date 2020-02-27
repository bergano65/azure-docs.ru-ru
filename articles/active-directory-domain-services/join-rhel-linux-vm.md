---
title: Присоединение виртуальной машины RHEL к доменным службам Azure AD | Документация Майкрософт
description: Узнайте, как настроить и присоединить Red Hat Enterprise Linuxную виртуальную машину к управляемому домену доменных служб Azure AD.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 1be9134ee217cb91461e89c9908b889a14ec0c3a
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613794"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Присоединение Red Hat Enterprise Linux виртуальной машины к управляемому домену доменных служб Azure AD

Чтобы разрешить пользователям входить на виртуальные машины в Azure с помощью одного набора учетных данных, можно присоединить виртуальные машины к домену Azure Active Directory доменных служб (AD DS). При присоединении виртуальной машины к управляемому домену AD DS Azure учетные записи пользователей и учетные данные из домена можно использовать для входа и управления серверами. Также применяются членства группы из управляемого домена Azure AD DS, позволяющие управлять доступом к файлам и службам на виртуальной машине.

В этой статье показано, как присоединить виртуальную машину Red Hat Enterprise Linux (RHEL) к управляемому домену AD DS Azure.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, по инструкциям из первого учебника [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Учетная запись пользователя, входящая в группу *администраторов Azure AD DC* в клиенте Azure AD.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Создание виртуальной машины RHEL Linux и подключение к ней

Если у вас есть существующая виртуальная машина RHEL Linux в Azure, подключитесь к ней с помощью SSH, а затем перейдите к следующему шагу, чтобы [приступить к настройке виртуальной машины](#configure-the-hosts-file).

Если необходимо создать ВИРТУАЛЬную машину RHEL Linux или создать тестовую виртуальную машину для использования с этой статьей, можно использовать один из следующих методов.

* [Портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

При создании виртуальной машины Обратите внимание на параметры виртуальной сети, чтобы убедиться, что виртуальная машина может обмениваться данными с управляемым доменом AD DS Azure:

* Разверните виртуальную машину в той же или в одноранговой виртуальной сети, в которой включены доменные службы Azure AD.
* Разверните виртуальную машину в другой подсети, чем экземпляр доменных служб Azure AD.

После развертывания виртуальной машины выполните действия по подключению к виртуальной машине по протоколу SSH.

## <a name="configure-the-hosts-file"></a>Настройка файла hosts

Чтобы убедиться, что имя узла виртуальной машины правильно настроено для управляемого домена, измените файл */etc/hosts* и задайте имя узла:

```console
sudo vi /etc/hosts
```

В файле *hosts* обновите адрес *localhost* . Рассмотрим следующий пример:

* *aaddscontoso.com* — это доменное DNS-имя управляемого домена AD DS Azure.
* *RHEL* — это имя узла ВИРТУАЛЬНОЙ машины RHEL, присоединяемой к управляемому домену.

Обновите эти имена собственными значениями:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

По завершении сохраните и закройте файл *hosts* с помощью команды `:wq` редактора.

## <a name="install-required-packages"></a>Установка необходимых пакетов

Виртуальной машине требуются дополнительные пакеты, чтобы присоединить виртуальную машину к управляемому домену Azure AD DS. Чтобы установить и настроить эти пакеты, обновите и установите средства присоединение к домену с помощью `yum`. Существуют некоторые различия между RHEL 7. x и RHEL 6. x, поэтому используйте соответствующие команды для версии дистрибутив в остальных разделах этой статьи.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Присоединение виртуальной машины к управляемому домену

Теперь, когда необходимые пакеты установлены на виртуальной машине, присоедините виртуальную машину к управляемому домену Azure AD DS. Опять же, выполните соответствующие действия для версии RHEL дистрибутив.

### <a name="rhel-7"></a>RHEL 7

1. Используйте команду `realm discover` для обнаружения управляемого домена AD DS Azure. В следующем примере обнаруживается *AADDSCONTOSO.com*области. Укажите собственное имя управляемого домена AD DS Azure в верхнем регистре:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Если команде `realm discover` не удается найти управляемый домен AD DS Azure, ознакомьтесь со следующими действиями по устранению неполадок.

    * Убедитесь, что домен доступен с виртуальной машины. Попробуйте `ping aaddscontoso.com`, чтобы проверить, возвращен ли положительный ответ.
    * Убедитесь, что виртуальная машина развернута в том же или в одноранговой виртуальной сети, в которой доступен управляемый домен Azure AD DS.
    * Убедитесь, что параметры DNS-сервера для виртуальной сети были обновлены, чтобы они указывали на контроллеры домена управляемого домена AD DS Azure.

1. Теперь инициализируйте Kerberos с помощью команды `kinit`. Укажите пользователя, который принадлежит к группе *администраторов контроллера домена AAD* . При необходимости [добавьте учетную запись пользователя в группу в Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Опять же, имя управляемого домена AD DS Azure необходимо вводить в верхнем регистре. В следующем примере учетная запись с именем `contosoadmin@aaddscontoso.com` используется для инициализации Kerberos. Введите собственную учетную запись пользователя, которая является членом группы *администраторов контроллера домена AAD* :

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Наконец, Присоедините компьютер к управляемому домену Azure AD DS с помощью команды `realm join`. Используйте ту же учетную запись пользователя, которая является членом группы *администраторов контроллера домена AAD* , указанной в предыдущей команде `kinit`, например `contosoadmin@AADDSCONTOSO.COM`.

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Присоединение виртуальной машины к управляемому домену Azure AD DS займет несколько секунд. В следующем примере выходных данных показано, что виртуальная машина успешно присоединена к управляемому домену Azure AD DS.

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL 6

1. Используйте команду `adcli info` для обнаружения управляемого домена AD DS Azure. В следующем примере обнаруживается *ADDDSCONTOSO.com*области. Укажите собственное имя управляемого домена AD DS Azure в верхнем регистре:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Если команде `adcli info` не удается найти управляемый домен AD DS Azure, ознакомьтесь со следующими действиями по устранению неполадок.

    * Убедитесь, что домен доступен с виртуальной машины. Попробуйте `ping aaddscontoso.com`, чтобы проверить, возвращен ли положительный ответ.
    * Убедитесь, что виртуальная машина развернута в том же или в одноранговой виртуальной сети, в которой доступен управляемый домен Azure AD DS.
    * Убедитесь, что параметры DNS-сервера для виртуальной сети были обновлены, чтобы они указывали на контроллеры домена управляемого домена AD DS Azure.

1. Сначала присоедините домен с помощью команды `adcli join`. Эта команда также создаст keytab для проверки подлинности компьютера. Используйте учетную запись пользователя, которая является членом группы *администраторов контроллера домена AAD* .

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Теперь настройте `/ect/krb5.conf` и создайте `/etc/sssd/sssd.conf` файлы для использования домена `aaddscontoso.com` Active Directory.
   Убедитесь, что `AADDSCONTOSO.COM` заменяется собственным именем домена:

    Откройте файл `/ect/krb5.conf` с помощью редактора:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Обновите файл `krb5.conf` в соответствии со следующим образцом:

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
    
   Создайте файл `/etc/sssd/sssd.conf`:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Обновите файл `sssd.conf` в соответствии со следующим образцом:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Убедитесь, что `/etc/sssd/sssd.conf` разрешений 600 и принадлежит привилегированному пользователю:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Используйте `authconfig`, чтобы указать виртуальной машине об интеграции с Linux в AD:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Запуск и включение службы SSSD:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Если виртуальная машина не может успешно завершить процесс присоединения к домену, убедитесь, что группа безопасности сети виртуальной машины разрешает исходящий трафик Kerberos для порта TCP + UDP 464 в подсети виртуальной сети для управляемого домена Azure AD DS.

Теперь проверьте, можно ли запрашивать сведения об Active Directory для пользователя с помощью `getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Разрешить проверку пароля для SSH

По умолчанию пользователи могут входить в виртуальную машину только с помощью проверки подлинности на основе открытых ключей SSH. Проверка подлинности на основе пароля завершается сбоем. При присоединении виртуальной машины к управляемому домену AD DS Azure эти учетные записи домена должны использовать проверку подлинности на основе пароля. Обновите конфигурацию SSH, чтобы разрешить проверку подлинности на основе пароля, как показано ниже.

1. Откройте файл *sshd_conf* с помощью редактора:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Обновите строку для *PasswordAuthentication* на *"Да"* :

    ```console
    PasswordAuthentication yes
    ```

    По завершении сохраните и закройте файл *sshd_conf* с помощью команды `:wq` редактора.

1. Чтобы применить изменения и позволить пользователям входить с помощью пароля, перезапустите службу SSH для RHEL дистрибутив версии:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Предоставление привилегий суперпользователя группе "Администраторы контроллера домена AAD"

Чтобы предоставить членам группы *администраторов контроллера домена AAD* права администратора на ВИРТУАЛЬНОЙ машине RHEL, добавьте запись в */etc/sudoers*. После добавления члены группы *администраторов контроллера домена AAD* могут использовать команду `sudo` на ВИРТУАЛЬНОЙ машине RHEL.

1. Откройте файл " *sudo* " для редактирования:

    ```console
    sudo visudo
    ```

1. Добавьте следующую запись в конец файла */etc/sudoers* . Группа *администраторов контроллера домена AAD* содержит пробелы в имени, поэтому включите escape-символ обратной косой черты в имя группы. Добавьте собственное доменное имя, например *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    По завершении сохраните и закройте редактор с помощью команды `:wq` редактора.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Вход в виртуальную машину с помощью учетной записи домена

Чтобы убедиться, что виртуальная машина успешно присоединена к управляемому домену Azure AD DS, запустите новое SSH-подключение, используя учетную запись пользователя домена. Убедитесь, что был создан корневой каталог и применяется членство в группе из домена.

1. Создайте новое SSH-подключение из консоли. Используйте учетную запись домена, принадлежащую к управляемому домену, с помощью команды `ssh -l`, например `contosoadmin@aaddscontoso.com`, а затем введите адрес виртуальной машины, например *RHEL.aaddscontoso.com*. При использовании Azure Cloud Shell Используйте общедоступный IP-адрес виртуальной машины, а не внутреннее DNS-имя.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. После успешного подключения к виртуальной машине убедитесь, что корневой каталог был инициализирован правильно:

    ```console
    pwd
    ```

    Вы должны находиться в каталоге */Home* с собственным каталогом, совпадающим с учетной записью пользователя.

1. Теперь убедитесь, что членство в группе разрешается правильно:

    ```console
    id
    ```

    Вы должны увидеть членство в группе из управляемого домена Azure AD DS.

1. Если вы вошли в виртуальную машину в качестве члена группы *администраторов контроллера домена AAD* , убедитесь, что вы можете правильно использовать команду `sudo`:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Следующие шаги

Если при подключении виртуальной машины к управляемому домену AD DS Azure или при входе с помощью учетной записи домена возникли проблемы, см. раздел [Устранение неполадок при присоединении к домену](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
