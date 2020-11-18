---
title: Обновление на месте образов Red Hat Enterprise Linux в Azure
description: Узнайте, как выполнить обновление на месте с образов Red Hat Enterprise 7. x до последней версии 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 1160bc43db0dc9ec1714b1766c8cadf09660e291
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844574"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux обновления на месте

В этой статье приводятся пошаговые инструкции по выполнению обновления на месте с Red Hat Enterprise Linux 7 до Red Hat Enterprise Linux 8 с помощью служебной программы "Леапп" в Azure. Во время обновления на месте существующая операционная система RHEL 7 заменяется версией RHEL 8.

>[!Note] 
> SQL Server в Red Hat Enterprise Linux предложениях не поддерживают обновление на месте в Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Что следует предполагать во время обновления
Во время обновления система перезагрузится несколько раз, и это нормально. При последней перезагрузке виртуальная машина будет обновлена до RHEL 8 последних версий. 

Процесс обновления может занять от 20 минут до нескольких часов. это зависит от нескольких факторов, таких как размер виртуальной машины и число пакетов, установленных в системе.

## <a name="preparations-for-the-upgrade"></a>Подготовка к обновлению
Обновление на месте — это официально рекомендуемый способ установки Red Hat и Azure, позволяющий клиентам обновлять систему до следующей основной версии. Прежде чем выполнять обновление, ознакомьтесь с разделом и примите во внимание. 

>[!Important] 
> Создайте моментальный снимок образа, прежде чем выполнять обновление.

>[!NOTE]
> Команды, приведенные в этой статье, необходимо выполнять с использованием учетной записи root.

* Убедитесь, что вы используете последнюю версию RHEL 7, которая в настоящее время RHEL 7,9. Если вы используете Заблокированную версию и не можете выполнить обновление до RHEL 7,9, можно выполнить [действия, описанные здесь, чтобы переключиться в репозиторий, не ЕУС](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Выполните приведенную ниже команду, чтобы получить сведения о том, как выполняется обновление и будет ли оно завершено. Команда должна создать файл в папке "/Вар/лог/леапп/leapp-report.txt", объясняющий процесс и выполняемые действия, а также если обновление возможно.
    ```bash
    leapp preupgrade --no-rhsm
    ```
* Убедитесь, что последовательная консоль работает, так как это позволяет отслеживать во время процесса обновления.

* Включение корневого доступа SSH в `/etc/ssh/sshd_config`
    1. Откройте файл `/etc/ssh/sshd_config`
    1. Выполните поиск по запросу "#PermitRootLogin да"
    1. Удалите "#", чтобы раскомментировать

## <a name="steps-for-performing-the-upgrade"></a>Шаги для выполнения обновления

Внимательно выполните эти действия. Безусловно, рекомендуется попробовать выполнить обновление на тестовом компьютере перед рабочими экземплярами.

1. Выполните обновление yum, чтобы получить последние пакеты клиентов.
    ```bash
    yum update -y
    ```

1. Установите леапп-Client-Package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Используйте файл леапп-Дата. tar. gz с repomap.csv и pes-events.jsв, который находится на [портале RedHat](https://access.redhat.com/articles/3664871), и извлеките их. 
    1. Скачайте файл.
    1. Извлеките содержимое и удалите файл с помощью следующей команды:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Добавьте файл "Answers" для "Леапп".
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Выполните обновление "Леапп".
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  После `leapp upgrade` успешного выполнения команды вручную перезагрузите систему, чтобы завершить процесс. Система перезагрузится несколько раз, в течение которых она будет недоступна. Отслеживайте процесс с помощью последовательной консоли.

1.  Убедитесь, что обновление успешно завершено.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. После завершения обновления удалите доступ с правами root SSH.
    1. Откройте файл `/etc/ssh/sshd_config`
    1. Выполните поиск по запросу "#PermitRootLogin да"
    1. Добавить "#" в комментарий

1. Перезапустите службу sshd, чтобы изменения вступили в силу
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>Распространенные проблемы
Это некоторые распространенные экземпляры, которые `leapp preupgrade` `leapp upgrade` могут завершиться сбоем или процессом.

**Ошибка: для следующих отключенных шаблонов подключаемых модулей совпадения не найдены**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**Решение**\
Отключите подключаемый модуль диспетчера подписки, изменив файл `/etc/yum/pluginconf.d/subscription-manager.conf` и выбрав параметр включено `enabled=0` .

Это вызвано включением подключаемого модуля Yum для диспетчера подписки, который не используется для виртуальных машин PAYG.

**Ошибка: возможные проблемы с удаленным входом с помощью root** `leapp preupgrade` Может произойти сбой со следующей ошибкой:
```structured-text
============================================================
                     UPGRADE INHIBITED
============================================================

Upgrade has been inhibited due to the following problems:
    1. Inhibitor: Possible problems with remote login using root account
Consult the pre-upgrade report for details and possible remediation.

============================================================
                     UPGRADE INHIBITED
============================================================
```
**Решение**\
Включите корневой доступ в `/etc/sshd_config` .
Это вызвано тем, что не включает доступ к корневому SSH в в `/etc/sshd_config` соответствии с разделом "[Подготовка к обновлению](#preparations-for-the-upgrade)". 

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [образах Red Hat в Azure](./redhat-images.md).
* Узнайте больше об [инфраструктуре обновления Red Hat](./redhat-rhui.md).
* Дополнительные сведения о [предложении RHEL BYOS](./byos.md).
* Сведения о процессах обновления Red Hat на месте можно найти в документации по Red Hat: [обновление с RHEL 7 до RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) .
* Сведения о политиках поддержки Red Hat для всех версий RHEL можно найти на странице [о жизненных циклах выпусков Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
