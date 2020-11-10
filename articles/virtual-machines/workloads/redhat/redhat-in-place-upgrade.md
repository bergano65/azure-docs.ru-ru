---
title: Обновление на месте образов Red Hat Enterprise Linux в Azure
description: Узнайте, как выполнить обновление на месте с образов Red Hat Enterprise 7. x до последней версии 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447902"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux обновления на месте

В этой статье приводятся пошаговые инструкции по выполнению обновления на месте с Red Hat Enterprise Linux 7 до Red Hat Enterprise Linux 8 с помощью служебной программы "Леапп" в Azure. Во время обновления на месте существующая операционная система RHEL 7 заменяется версией RHEL 8.

>[!Note] 
> SQL Server в Red Hat Enterprise Linux предложениях не поддерживают обновление на месте в Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Что следует предполагать во время обновления
Во время обновления система перезагрузится несколько раз, и это нормально. При последней перезагрузке виртуальная машина будет обновлена до RHEL 8 последних версий.

## <a name="preparations-for-the-upgrade"></a>Подготовка к обновлению
Обновления на месте являются официально рекомендуемым способом для Red Hat и Azure, что позволяет клиентам обновить систему до следующей основной версии. Прежде чем выполнять обновление, ознакомьтесь с разделом и примите во внимание. 

>[!Important] 
> Создайте моментальный снимок образа, прежде чем выполнять обновление.

>[!NOTE]
> Команды, приведенные в этой статье, необходимо выполнять с использованием учетной записи root.

* Убедитесь, что вы используете последнюю версию RHEL 7, которая в настоящее время RHEL 7,9. Если вы используете Заблокированную версию и не можете выполнить обновление до RHEL 7,9, можно выполнить [действия, описанные здесь, чтобы переключиться в репозиторий, не ЕУС](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Выполните приведенную ниже команду, чтобы получить сведения о том, как выполняется обновление и будет ли оно завершено. Команда должна создать файл в папке "/Вар/лог/леапп/leapp-report.txt", объясняющий процесс и выполняемые действия, а также если обновление возможно.
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Шаги для выполнения обновления

Внимательно выполните эти действия. Безусловно, рекомендуется попробовать выполнить обновление на тестовом компьютере перед рабочими экземплярами.

1. Выполните обновление yum, чтобы получить последние пакеты клиентов.
    ```bash
    yum update
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
    
1. Включение Пермитрутлогин в каталог/etc/SSH/sshd_config
    1. Откройте файл каталог/etc/SSH/sshd_config
    1. Выполните поиск по запросу "#PermitRootLogin да"
    1. Удалите "#", чтобы раскомментировать



1. Выполните обновление "Леапп".
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Перезапустите службу sshd, чтобы изменения вступили в силу
    ```bash
    systemctl restart sshd
    ```
1. Повторно закомментируйте Пермитрутлогин в каталог/etc/SSH/sshd_config
    1. Откройте файл каталог/etc/SSH/sshd_config
    1. Выполните поиск по запросу "#PermitRootLogin да"
    1. Добавить "#" в комментарий

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [образах Red Hat в Azure](./redhat-images.md).
* Узнайте больше об [инфраструктуре обновления Red Hat](./redhat-rhui.md).
* Дополнительные сведения о [предложении RHEL BYOS](./byos.md).
* Сведения о процессах обновления Red Hat на месте можно найти в документации по Red Hat: [обновление с RHEL 7 до RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) .
* Сведения о политиках поддержки Red Hat для всех версий RHEL можно найти на странице [о жизненных циклах выпусков Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).