---
title: Расширенная поддержка жизненного цикла в Red Hat Enterprise Linux
description: Дополнительные сведения о добавлении поддержки расширенного жизненного цикла выпуска Red Hat Enterprise
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 1f31cc982e5e7f92cfe7e8adf588dd96628f2c6f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95744047"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Расширенная поддержка жизненного цикла Red Hat Enterprise Linux (RHEL)
В этой статье приводятся сведения о поддержке расширенного жизненного цикла для корпоративных образов Red Hat.
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Жизненный цикл Red Hat Enterprise Linux 6
Начиная с 30 ноября 2020 Red Hat Enterprise Linux 6 будет достигнут конец этапа обслуживания. За этапом обслуживания следует Расширенный жизненный цикл. Как Red Hat Enterprise Linux 6 переходов с этапов полного или технического обслуживания, настоятельно рекомендуется обновление до Red Hat Enterprise Linux 7 или 8. Если клиенты должны остаться в Red Hat Enterprise Linux 6, рекомендуется добавить надстройку поддержки Red Hat Enterprise Linux расширенного жизненного цикла (ELS).

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Действия по добавлению поддержки расширенного жизненного цикла для виртуальных машин с оплатой по мере использования в Marketplace
1. Заполните [форму ELS, доступную здесь](https://aka.ms/els-form) , указав контактные данные и сведения о подписке на виртуальных машинах, для которых требуется добавить поддержку ELS. Дополнительные сведения о ценах также доступны в форме.
1. Группа Azure Red Hat Enterprise Linux будет обращаться к вам со списком виртуальных машин для поддержки ELS в течение 1-2 рабочих дней. Ознакомьтесь со списком и ответьте на согласие на добавление цен.
1. Команда Azure Red Hat Enterprise Linux поможет вам добавить клиентский пакет ELS на виртуальные машины. Выполните действия, которые будут указаны в сообщении электронной почты, чтобы продолжить получение сведений об обслуживании программного обеспечения (исправления ошибок и безопасности) и поддержку Red Hat Enterprise Linux 6.

> [!Note]
> Не предоставляйте общий доступ к шагам для использования RHEL ELS Add on to a of the, за пределами Организации. AzureRedHatELS@microsoft.comОбратитесь в службу поддержки или за дополнительными вопросами.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Я использую Red Hat Enterprise Linux 6 и не может выполнить миграцию на более позднюю версию в настоящее время. Какие существуют варианты?
* Продолжайте работать Red Hat Enterprise Linux 6 и приобретайте поддержку расширенного жизненного цикла (ELS) Add-On репозитории, чтобы по-прежнему получить ограниченное обслуживание программного обеспечения и техническую поддержку (см. статью Обновление и сведения о ценах ниже).
* Выполните миграцию на Red Hat Enterprise Linux 7 или 8 как можно скорее.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Какова дополнительная плата за использование Red Hat Enterprise Linux надстройки поддержки расширенного жизненного цикла (ELS)?
Затраты, связанные с расширенной поддержкой жизненного цикла, можно найти в [форме ELS](https://aka.ms/els-form) .


## <a name="next-steps"></a>Следующие шаги

* Полный список образов RHEL в Azure см. в статье [образы Red Hat Enterprise Linux (RHEL), доступные в Azure](./redhat-imagelist.md).
* Дополнительные сведения о инфраструктуре обновления Red Hat для Azure см. в разделе [инфраструктура обновления Red Hat для виртуальных машин RHEL по запросу в Azure](./redhat-rhui.md).
* Дополнительные сведения о предложении RHEL BYOS см. [в статье Red Hat Enterprise Linux собственные образы на основе собственных подписок в Azure](./byos.md).
* Сведения о политиках поддержки Red Hat для всех версий RHEL см. в разделе [жизненный цикл Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).