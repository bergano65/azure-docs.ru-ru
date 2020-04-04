---
title: Присоединитесь к ubuntu VM в службу домена Azure AD Domain Services Документы Майкрософт
description: Узнайте, как настроить и присоединиться к виртуальной машине Ubuntu Linux к домену Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 95373ab8ff78c5bcb856e6d7e6d67d8525cd3f7e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655135"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Присоединиться к виртуальной машине Ubuntu Linux к домену Azure AD Domain Services, управляемому

Чтобы позволить пользователям войти в виртуальные машины (Виртуальные технологии) в Azure, используя единый набор учетных данных, можно присоединиться к виртуальным всасданным службам функционального каталога Azure (AD DS), управляемым доменом. При присоединении к VM к домену Azure AD DS, учетные записи пользователей и учетные данные домена могут использоваться для ввоза и управления серверами. Членство в группе из домена Azure AD DS также применяется, чтобы позволить вам контролировать доступ к файлам или службам на VM.

В этой статье показано, как присоединиться к Ubuntu Linux VM к домену Azure AD DS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас нет подписки НаAz, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, по инструкциям из первого учебника [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Учетная запись пользователя, войдя часть домена Azure AD DS.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Создание и подключение к Ubuntu Linux VM

Если у вас есть существующий Ubuntu Linux VM в Azure, подключитесь к нему с помощью SSH, а затем перейдите на следующий шаг, чтобы [начать настройку VM.](#configure-the-hosts-file)

Если вам нужно создать Ubuntu Linux VM, или хотите создать тест VM для использования в этой статье, вы можете использовать один из следующих методов:

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
* *ubuntu* — это хост-имя вашего Ubuntu VM, к которому вы присоединяетесь к управляемому домену.

Обновление этих имен с вашими собственными значениями:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

При этом сохраняй и выходизьте файл *хоста,* `:wq` используя команду редактора.

## <a name="install-required-packages"></a>Установка необходимых пакетов

VM нужны дополнительные пакеты, чтобы присоединиться к VM в домене Azure AD DS. Для установки и настройки этих пакетов, обновить и установить домен-соединение инструментов с использованием`apt-get`

Во время установки Kerberos пакет *krb5-пользователя* подсказывает имя царства в ALL UPPERCASE. Например, если имя вашего домена Azure AD DS *находится aaddscontoso.com,* введите *AADDSCONTOSO.COM* как область. Установка записывает `[realm]` `[domain_realm]` и разделы в */etc/krb5.conf* файл конфигурации. Убедитесь, что вы указать области ВСЕ UPPERCASE:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Протокол настройки сетевого времени (NTP)

Для правильной работы доменного сообщения дата и время вашего Ubuntu VM должны синхронизироваться с управляемым доменом Azure AD DS. Добавьте в файл */etc/ntp.conf* управляемый домен, управляемый Azure AD DS.

1. Откройте файл *ntp.conf* с редактором:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. В файле *ntp.conf* создайте строку для добавления DNS-имени DNS-динора DNS управляемого DNS домена Azure AD. В следующем примере добавляется запись для *aaddscontoso.com.* Используйте свое собственное имя DNS:

    ```console
    server aaddscontoso.com
    ```

    При этом сохраните и выйдите из `:wq` файла *ntp.conf,* используя команду редактора.

1. Чтобы убедиться, что VM синхронизирован с доменом Azure AD DS, необходимы следующие шаги:

    * Остановка сервера NTP
    * Обновление даты и времени из управляемого домена
    * Запуск службы NTP

    Выполнить следующие команды для завершения этих шагов. Используйте свое собственное `ntpdate` имя DNS с командой:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Присоединиться к VM к управляемому домену

Теперь, когда необходимые пакеты установлены на VM и NTP настроены, присоединяйтесь к VM к домену Azure AD DS.

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
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Требуется несколько минут, чтобы присоединиться к VM в домене Azure AD DS. Следующий пример вывода показывает, что VM успешно присоединился к домену Azure AD DS:

```output
Successfully enrolled machine in realm
```

Если ваш VM не может успешно завершить процесс соединения домена, убедитесь, что группа сетевой безопасности VM позволяет исходящих Kerberos трафика на TCP и UDP порт 464 в виртуальную сеть подсети для вашего Azure AD DS управляемых домена.

## <a name="update-the-sssd-configuration"></a>Обновление конфигурации SSSD

Один из пакетов, установленных на предыдущем этапе, был для Системной Службы Безопасности Daemon (SSSD). Когда пользователь пытается войти в VM, используя учетные данные домена, SSSD передает запрос поставщику аутентификации. В этом сценарии SSSD использует Azure AD DS для проверки подлинности запроса.

1. Откройте файл *sssd.conf* с редактором:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Комментарий в строке для *use_fully_qualified_names* следующим образом:

    ```console
    # use_fully_qualified_names = True
    ```

    При этом сохраните и выйдите из файла `:wq` *sssd.conf,* используя команду редактора.

1. Чтобы применить изменение, перезапустите службу SSSD:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Настройка учетной записи пользователя и параметров группы

С присоединении VM к домену Azure AD DS и настроенном для проверки подлинности, необходимо выполнить несколько вариантов конфигурации пользователя. Эти изменения конфигурации включают разрешение проверки подлинности на основе паролей и автоматическое создание домашних каталогов на локальном VM при первом входе в систему пользователей домена.

### <a name="allow-password-authentication-for-ssh"></a>Разрешить проверку подлинности пароля для SSH

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

1. Чтобы применить изменения и позволить пользователям войти в систему с помощью пароля, перезапустите службу SSH:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Настройка автоматического создания домашнего каталога

Для автоматического создания домашнего каталога при первом входе пользователя в систему, выполните следующие шаги:

1. Откройте файл */etc/pam.d/common-session* в редакторе:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Добавьте следующую строку в `session optional pam_sss.so`этом файле ниже строки:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    При этом сохраньте и выйдите `:wq` из файла *общей сессии,* используя команду редактора.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Предоставление привилегий суперпользователя группе "Администраторы контроллера домена AAD"

Чтобы предоставить членам *AAD DC Администраторы группы административных* привилегий на Ubuntu VM, вы добавляете запись *на / и т.д. / судографов*. После добавления члены *группы администраторов AAD DC* могут использовать `sudo` команду на Ubuntu VM.

1. Откройте файл *sudoers* для редактирования:

    ```console
    sudo visudo
    ```

1. Добавьте следующую запись в конец *файла /etc/sudoers:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    При этом сохраните и выйдите из редактора с помощью `Ctrl-X` команды.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Войти на VM с помощью учетной записи домена

Чтобы убедиться, что VM был успешно подключен к домену Azure AD DS, запустите новое соединение SSH с помощью учетной записи пользователя домена. Подтвердите, что домашний каталог был создан, и что членство в группе из домена применяется.

1. Создайте новое соединение SSH с консоли. Используйте учетную запись домена, которая `ssh -l` принадлежит управляемому домену, используя команду, `contosoadmin@aaddscontoso.com` например, а затем введите адрес вашего VM, например *ubuntu.aaddscontoso.com.* Если вы используете Лазурную облачную оболочку, используйте общедоступный IP-адрес VM, а не внутреннее имя DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
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
    sudo apt-get update
    ```

## <a name="next-steps"></a>Дальнейшие действия

Если у вас возникли проблемы с подключением VM к домену Azure [Troubleshooting domain join issues](join-windows-vm.md#troubleshoot-domain-join-issues)AD DS или вовремя с помощью учетной записи домена, см.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
