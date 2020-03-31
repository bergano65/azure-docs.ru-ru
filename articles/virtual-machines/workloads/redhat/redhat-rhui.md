---
title: Инфраструктура обновления Red Hat Update Infrastructure | Документация Майкрософт
description: Узнайте об инфраструктуре обновления Red Hat Update Infrastructure для предоставляемых по запросу экземпляров Red Hat Enterprise Linux в Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: aa9fd230f59b5e46576e78beb0436c85449d3c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256918"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure для предоставляемых по запросу виртуальных машин Red Hat Enterprise Linux в Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) позволяет поставщикам облачных служб (например, Azure) создавать зеркальные копии размещенного с помощью Red Hat содержимого репозитория, создавать пользовательские репозитории с содержимым для Azure и предоставлять пользовательским виртуальным машинам доступ к этому содержимому.

Образы Red Hat Enterprise Linux (RHEL) с оплатой по мере использования (PAYG) предварительно настроены для доступа к Azure RHUI. Никаких дополнительных настроек не требуется. Чтобы получить последние обновления, выполните `sudo yum update`, когда экземпляр RHEL будет готов. Плата за эту службу входит в стоимость программного обеспечения RHEL (PAYG).

Дополнительные сведения об образах RHEL в Azure, включая политики публикации и хранения, можно найти [здесь](./redhat-images.md).

Сведения о политиках поддержки Red Hat для всех версий RHEL можно найти на странице [о жизненных циклах выпусков Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

> [!IMPORTANT]
> RHUI предназначен только для изображений с оплатой по мере использования (PAYG). Для пользовательских и золотых изображений, также известный как принести-ваш-собственный подписки (BYOS), система должна быть прикреплена к RHSM или спутник для того, чтобы получать обновления. Более подробную информацию можно узнать в [статье Red Hat.](https://access.redhat.com/solutions/253273)


## <a name="important-information-about-azure-rhui"></a>Важные сведения об Azure RHUI

* Azure RHUI — это инфраструктура обновления, которая поддерживает все vMs RHEL PAYG, созданные в Azure. Это не исключает вас от регистрации PAYG RHEL VMs с менеджером подписки или спутником или другим источником обновлений, но это с PAYG VM приведет к косвенному двойному выставлению счетов. Подробности приведены ниже.
* В стоимость образа RHEL (PAYG) входит плата за доступ к инфраструктуре RHUI, размещенной в Azure. Отмена регистрации виртуальной машины RHEL (PAYG) в инфраструктуре RHUI в Azure не приведет к ее преобразованию в виртуальную машину с использованием собственной лицензии (BYOL). В случае регистрации одной и той же виртуальной машины в другом источнике обновлений вы можете нести двойные _косвенные_ расходы. Первая плата будет взиматься за использование программного обеспечения Azure RHEL. Вторая плата будет взиматься за подписки Red Hat, которые были приобретены ранее. Если вам постоянно требуется использовать инфраструктуру обновления, помимо RHUI, размещенную на базе Azure, подумайте о регистрации для использования [изображений RHEL BYOS.](./byos.md)

* Образы RHEL (SAP PAYG) в Azure (RHEL for SAP, RHEL for SAP HANA и RHEL for SAP Business Applications) подключаются к выделенным каналам RHUI, которые остаются на определенной версии RHEL с дополнительным номером, что требуется для сертификации SAP.

* Доступ к размещенной в Azure инфраструктуре RHUI могут получать виртуальные машины с IP-адресами в рамках [диапазонов IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653). Если весь трафик виртуальной машины перенаправляется через локальную сетевую инфраструктуру, может потребоваться настройка определяемых пользователем маршрутов, чтобы виртуальные машины RHEL (PAYG) могли получить доступ к инфраструктуре RHUI в Azure. В этом случае для _всех_ IP-адресов RHUI необходимо будет добавить определенные пользователями маршруты.


## <a name="image-update-behavior"></a>Поведение обновления изображения

По состоянию на апрель 2019 года Azure предлагает изображения RHEL, которые подключены к репозиториям расширенной поддержки обновлений (EUS) по умолчанию и изображениям RHEL, которые по умолчанию подключены к регулярным (не EUS) репозиториям. Более подробная информация о RHEL EUS доступна в [документации жизненного цикла версии](https://access.redhat.com/support/policy/updates/errata) Red Hat и [документации EUS.](https://access.redhat.com/articles/rhel-eus) Поведение по `sudo yum update` умолчанию будет варьироваться в зависимости от того, какое изображение RHEL вы подготовлены, так как различные изображения подключены к различным репозиториям.

Для получения полного `az vm image list --publisher redhat --all` списка изображений выполнить с помощью Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Изображения, подключенные к репозиториям, не входящих в EUS

Если вы предоставите VM из изображения RHEL, которое подключено к репозиториям, не входящих `sudo yum update`в EUS, вы будете обновлены до последней незначительной версии RHEL при запуске. Например, если вы предоставляете VM от RHEL 7.4 PAYG изображения и запустить `sudo yum update`, вы в конечном итоге с RHEL 7.7 VM (последняя незначительная версия в семье RHEL7).

Изображения, подключенные к репозиториям, не входящих в EUS, не будут содержать незначительный номер версии в SKU. SKU является третьим элементом в URN (полное название изображения). Например, все следующие изображения прикрепляются к репозиториям, не входящих в ЕСС:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Обратите внимание, что SkUs являются либо 7-LVM или 7-RAW. Незначительная версия указана в версии (четвертый элемент в URN) этих изображений.

### <a name="images-connected-to-eus-repositories"></a>Изображения, подключенные к репозиториям EUS

Если вы предоставляете VM изображение RHEL, подключенное к репозиториям EUS, вы не будете `sudo yum update`обновлены до последней незначительной версии RHEL при запуске. Это связано с тем, что изображения, подключенные к репозиториям EUS, также заблокированы для их конкретной незначительной версии.

Изображения, подключенные к репозиториям EUS, будут содержать номер незначительной версии в SKU. Например, все следующие изображения прикрепляются к репозиториям EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>Предложение RHEL EUS и фиксация версии виртуальных машин RHEL

Расширенные репозитории поддержки обновления (EUS) доступны для клиентов, которые могут захотеть заблокировать свои RHEL VMs на определенный minor-релиз RHEL после подготовки VM. Вы можете зафиксировать определенный дополнительный номер версии для виртуальной машины RHEL, изменив репозитории таким образом, чтобы они указывали на репозитории Extended Update Support (EUS). Вы также можете отменить операцию блокировки версий EUS.

>[!NOTE]
> EUS не поддерживается на RHEL Extras. Это означает, что если вы устанавливаете пакет, который обычно доступен из канала RHEL Extras, вы не сможете сделать это во время eUS. Red Hat Extras Продукт жизненный цикл подробно [описан здесь](https://access.redhat.com/support/policy/updates/extras/).

На момент написания этой статьи поддержка EUS закончилась для RHEL <7,4 евро. Для получения более подробной информации смотрите раздел "Red Hat Enterprise Linux Longer Support Add-Ons" в [документации Red Hat.](https://access.redhat.com/support/policy/updates/errata/)
* Поддержка RHEL 7.4 EUS заканчивается 31 августа 2019 г.
* Поддержка RHEL 7.5 EUS заканчивается 30 апреля 2020 г.
* Поддержка RHEL 7.6 EUS заканчивается 31 октября 2020 г.
* Поддержка RHEL 7.7 EUS заканчивается 30 августа 2021 г.

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Переключите RHEL VM на EUS (блокировка версии на конкретную незначительную версию)
Используйте следующие инструкции, чтобы заблокировать RHEL VM для конкретного незначительного выпуска (запуск в качестве корня):

>[!NOTE]
> Это применяется только для версий RHEL, для которых доступна EUS. На момент написания этой статьи, это включает RHEL 7.2-7.7. Дополнительные сведения можно найти на странице [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata) (Жизненный цикл Red Hat Enterprise Linux).

1. Отключите репозитории без поддержки EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Добавьте репозитории EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Блокировка `releasever` переменной (запуск в корне):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Выполнив приведенные выше инструкции, вы зафиксируете текущий дополнительный номер версии в качестве дополнительного номера версии RHEL. Введите конкретный дополнительный номер версии, если вам нужно обновить систему и зафиксировать дополнительный номер версии, который не является последним. Например, команда `echo 7.5 > /etc/yum/vars/releasever` зафиксирует версию RHEL 7.5

1. Обновление виртуальной машины RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Переключить RHEL VM обратно в не-EUS (удалить блокировку версии)
Выполнить следующее в качестве корня:
1. Удалите `releasever` файл:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Отключить РЕПО EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Настройка RHEL VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Обновление виртуальной машины RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP-адреса для серверов доставки содержимого RHUI

Инфраструктура RHUI поддерживается во всех регионах, где доступны предоставляемые по запросу образы RHEL. Сейчас в этот список входят общедоступные регионы, указанные на странице [состояния Azure](https://azure.microsoft.com/status/), регионы, обслуживающие государственные организации США, и регионы Microsoft Azure — Германия.

При использовании конфигурации сети для усиленного ограничения доступа с виртуальных машин RHEL (PAYG) убедитесь, что для приведенных ниже IP-адресов разрешена операция `yum update`, в зависимости от используемой среды.


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Инфраструктура Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Обновление сертификата клиента RHUI с истекшим сроком действия на виртуальной машине

Если вы используете более старое изображение RHEL VM, например, `RedHat:RHEL:7.4:7.4.2018010506`RHEL 7.4 (изображение URN: ), вы столкнетесь с проблемами подключения к RHUI из-за просроченного сертификата клиента TLS/SSL. Ошибка, которую вы видите, может выглядеть как _"SSL-эксперт отклонил ваш сертификат как истекший"_ или _"Ошибка: не может получить метаданные репозитория (repomd.xml) для репозитория: ... Пожалуйста, проверьте его путь и попробуйте еще раз "._ Чтобы решить эту проблему, обновите пакет клиента RHUI на виртуальной машине, используя следующую команду.

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Кроме того, с помощью команды `sudo yum update` можно также обновить пакет сертификата клиента (в зависимости от версии RHEL), несмотря на ошибки "Срок действия SSL-сертификата истек", которые будут отображаться для других репозиториев. После успешного обновления нормальное подключение к другим репозиториям RHUI должно быть восстановлено, как и возможность успешного выполнения команды `sudo yum update`.

Если вы столкнулись с ошибкой `yum update`404 во время выполнения, попробуйте следующее, чтобы обновить кэш yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Устранение неполадок подключения к инфраструктуре RHUI в Azure
Если вы столкнулись с проблемами при подключении к инфраструктуре RHUI в Azure с виртуальной машины RHEL (PAYG) в Azure, выполните следующие действия.

1. Проверьте конфигурацию виртуальной машины для конечной точки RHUI в Azure.

    1. Проверьте, содержит ли файл `/etc/yum.repos.d/rh-cloud.repo` ссылку на `rhui-[1-3].microsoft.com` в `baseurl` раздела `[rhui-microsoft-azure-rhel*]`. Если это так, то вы используете новую версию инфраструктуры RHUI Azure.

    1. Если он указывает на расположение с помощью шаблона `mirrorlist.*cds[1-4].cloudapp.net`, то требуется обновить конфигурацию. Вы используете устаревший моментальный снимок виртуальной машины. Его нужно обновить, чтобы в файле было указано расположение новой версии инфраструктуры RHUI в Azure.

1. Доступ к размещенной в Azure инфраструктуре RHUI могут получать только виртуальные машины с [IP-адресами в диапазонах центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Если вы убедились, что IP-адрес подключаемой виртуальной машины находится в диапазоне IP-адресов Azure, но при использовании новой конфигурации вам по-прежнему не удается подключиться к инфраструктуре RHUI в Azure, обратитесь в службу поддержки Майкрософт или Red Hat.

### <a name="infrastructure-update"></a>Обновление инфраструктуры

В сентябре 2016 года была развернута обновленная инфраструктура RHUI в Azure. В апреле 2017 года работа старой инфраструктуры RHUI в Azure была завершена. Если вы использовали образы RHEL (PAYG) либо их моментальные снимки с сентября 2016 года или более позднего времени, вы автоматически подключились к новой версии инфраструктуры RHUI в Azure. Но если у вас есть более старые моментальные снимки виртуальных машин, их конфигурации необходимо обновить вручную, чтобы обеспечить доступ к инфраструктуре RHUI в Azure, как описано в следующем разделе.

Новые серверы RHUI в Azure развертываются с помощью [диспетчера трафика Azure](https://azure.microsoft.com/services/traffic-manager/). В диспетчере трафика одну конечную точку (rhui-1.microsoft.com) может использовать любая виртуальная машина независимо от региона.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Процедура обновления вручную для использования серверов Azure RHUI
Эта процедура приводится только для справки. Образы RHEL (PAYG) уже содержат правильную конфигурацию для подключения к инфраструктуре RHUI в Azure. Чтобы вручную обновить конфигурацию для использования серверов RHUI в Azure, выполните следующие действия.

- Для RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Для RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Для RHEL 8:
    1. Создание файла конфигурации:
        ```bash
        vi rhel8.config
        ```
    1. Добавьте следующее содержимое в файл конфигурации:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Сохранить файл и запустить следующую команду:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Обновление ВМ
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Дальнейшие действия
* Сведения о создании виртуальной машины Red Hat Enterprise Linux на основе образа с оплатой по мере использования (PAYG) и о применении размещенной в Azure инфраструктуры RHUI см. на странице [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Дополнительные сведения об образах Red Hat в Azure можно найти на [странице документации](./redhat-images.md).
* Сведения о политиках поддержки Red Hat для всех версий RHEL можно найти на странице [о жизненных циклах выпусков Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
