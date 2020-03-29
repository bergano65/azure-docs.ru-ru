---
title: Присоединитесь к сервису доменов CentOS VM к службам домена Azure AD (ru) Документы Майкрософт
description: Узнайте, как настроить и присоединиться к виртуальной машине CentOS Linux к домену Azure AD Domain Services, управляемому доменом.
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
ms.openlocfilehash: c634b1288727ae045d1fb8b6f6cdff4a80e757ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298911"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Присоединиться к виртуальной машине CentOS Linux к домену Azure AD Domain Services, управляемому

Чтобы позволить пользователям войти в виртуальные машины (Виртуальные технологии) в Azure, используя единый набор учетных данных, можно присоединиться к виртуальным всасданным службам функционального каталога Azure (AD DS), управляемым доменом. При присоединении к VM к домену Azure AD DS, учетные записи пользователей и учетные данные домена могут использоваться для ввоза и управления серверами. Членство в группе из домена Azure AD DS также применяется, чтобы позволить вам контролировать доступ к файлам или службам на VM.

В этой статье показано, как присоединиться к CentOS Linux VM к домену Azure AD DS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, по инструкциям из первого учебника [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Учетная запись пользователя, войдя в домен Azure AD DS.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Создание и подключение к CentOS Linux VM

Если у вас есть существующий CentOS Linux VM в Azure, подключитесь к нему с помощью SSH, а затем перейдите на следующий шаг, чтобы [начать настройку VM.](#configure-the-hosts-file)

Если вам нужно создать CentOS Linux VM, или хотите создать тест VM для использования в этой статье, вы можете использовать один из следующих методов:

* [Портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [Лазурный CLI](../virtual-machines/linux/quick-create-cli.md)
* [Лазурная силаШелл](../virtual-machines/linux/quick-create-powershell.md)

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
* *centos* — это хост-имя вашего CentOS VM, к которому вы присоединяетесь к управляемому домену.

Обновление этих имен с вашими собственными значениями:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

При этом сохраняй и выходизьте файл *хоста,* `:wq` используя команду редактора.

## <a name="install-required-packages"></a>Установка необходимых пакетов

VM нужны дополнительные пакеты, чтобы присоединиться к VM в домене Azure AD DS. Для установки и настройки этих пакетов, обновить `yum`и установить домен-соединение инструментов с использованием:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Присоединиться к VM к управляемому домену

Теперь, когда необходимые пакеты установлены на VM, присоединяйтесь к VM к домену Azure AD DS.

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

Если ваш VM не может успешно завершить процесс соединения домена, убедитесь, что группа сетевой безопасности VM позволяет исходящих Kerberos трафика на TCP и UDP порт 464 в виртуальную сеть подсети для вашего Azure AD DS управляемых домена.

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

1. Чтобы применить изменения и позволить пользователям войти в систему с помощью пароля, перезапустите службу SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Предоставление привилегий суперпользователя группе "Администраторы контроллера домена AAD"

Чтобы предоставить членам *AAD DC Администраторы группы административных* привилегий на CentOS VM, вы добавляете запись *на / и т.д. / судосов*. После добавления члены *группы администраторов AAD DC* могут использовать `sudo` команду на CentOS VM.

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

1. Создайте новое соединение SSH с консоли. Используйте учетную запись домена, которая `ssh -l` принадлежит управляемому домену, используя команду, `contosoadmin@aaddscontoso.com` например, а затем введите адрес вашего VM, *например, centos.aaddscontoso.com.* Если вы используете Лазурную облачную оболочку, используйте общедоступный IP-адрес VM, а не внутреннее имя DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
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
