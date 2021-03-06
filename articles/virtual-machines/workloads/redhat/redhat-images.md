---
title: Образы Red Hat Enterprise Linux в Azure | Документация Майкрософт
description: Дополнительные сведения об образах Red Hat Enterprise Linux в Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 27cd5b775fbd2af58d93d539420262665d70ead4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486330"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Общие сведения об образах Red Hat Enterprise Linux
В этой статье описываются доступные образы Red Hat Enterprise Linux (RHEL) в Azure Marketplace, а также политики их именования и хранения.

Сведения о политиках поддержки Red Hat для всех версий RHEL можно найти на странице [о жизненных циклах выпусков Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata). Сведения о ценах см. в [калькуляторе цен Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Образы RHEL сейчас доступны в службе поддержки Azure Marketplace моделей лицензирования "Создание собственной подписки" (BYOS) или "Оплата по мере использования" (PAYG). [AHB](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) и динамическое переключение между BYOS и PAYG не поддерживается. Переключение режима лицензирования требует повторного развертывания виртуальной машины из соответствующего образа.

>[!NOTE]
> Для устранения любых проблем, связанных с образами RHEL в Azure Marketplace, отправьте запрос в службу поддержки корпорации Майкрософт.

## <a name="images-available-in-azure"></a>Образы, доступные в Azure
При поиске "Red Hat" в Marketplace или при создании ресурса в портал Azure пользовательском интерфейсе вы увидите только подмножество всех доступных образов RHEL. Вы всегда можете получить полный набор доступных образов виртуальных машин с помощью Azure CLI, PowerShell или API.

Чтобы просмотреть полный набор доступных образов Red Hat в Azure, выполните следующую команду.

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Соглашение об именовании
Образы виртуальных машин в Azure упорядочены по издателям, предложениям, номерам SKU и версиям. Сочетание "Издатель:предложение:номер SKU:версия" — это уникальный номер (URN) образа, который однозначно определяет используемый образ.

Например, `RedHat:RHEL:7-LVM:7.6.2018103108` ссылается на образ RHEL 7,6 с LVM секционирования, созданный 31 октября 2018 г.

Ниже приведен пример создания виртуальной машины RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Моникер latest
REST API Azure позволяет использовать моникер "latest" для версии вместо конкретной версии. При указании моникера latest будут подготовлены последние доступные образы для заданного издателя, предложения и SKU.

Например, `RedHat:RHEL:7-LVM:latest` ссылается на новейшее доступное семейство RHEL 7 с LVM Partition.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> Как правило, при сравнении версий для определения последней версии используются правила [метода CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="rhel-6-image-types"></a>Типы образов RHEL 6
Для изображений RHEL 6. x используются следующие типы изображений:

|Издатель | Предложение | Значение номера SKU | Версия | Сведения
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Дополнительный номер версии (например, 6,9) | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 6.9.2018010506) | Все стандартные образы RHEL 6. x соответствуют этому соглашению
|RedHat | RHEL — BYOS | RHEL — raw69 | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 6.9.20181023) | Этот образ является образом RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 6.8.2017053118) | Это образ RHEL 6,8 для приложений SAP. Он имеет право доступа к репозиториям приложений SAP, а также к базовым репозиториям RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 6.7.2017053121) | Это RHEL 6,7 для образа SAP HANA. Он имеет право доступа к SAP HANA репозиториям, а также к базовым репозиториям RHEL.

### <a name="rhel-7-image-types"></a>Типы изображений RHEL 7
Для изображений RHEL 7. x существует несколько различных типов изображений. В следующей таблице показаны различные наборы предоставляемых образов. Полный список можно просмотреть с помощью команды AZ CLI `az vm image list --publisher redhat --all`.

>[!NOTE]
> Если не указано иное, все образы LVM секционированы и будут подключаться к регулярным репозиториям RHEL (т. е. не ЕУС, а не E4S). Перейдем к публикации только LVMных образов, но они открыты для отзывов об этом решении. Подробные сведения о поддержке расширенных обновлений и службах обновления для SAP доступны на [странице Red Hat Enterprise Linux жизненный цикл](https://access.redhat.com/support/policy/updates/errata).

|Издатель | Предложение | Значение номера SKU | Версия | Сведения
|----------|-------|------------|---------|--------
|RedHat | RHEL | Дополнительный номер версии (например, 7,6) | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.6.2019102813) | Образы, опубликованные до апреля 2019, будут подключены к стандартным репозиториям RHEL. Образы, опубликованные после апреля 2019, будут подключены к репозиториям расширенной поддержки обновлений Red Hat (ЕУС), чтобы разрешить блокировку версий определенного дополнительного номера версии. Клиенты, которым требуются обычные репозитории, должны использовать образы, содержащие 7-LVM или 7-RAW в значении SKU (подробности приведены ниже). Образы RHEL 7,7 и более поздних версий будут LVM секционированы. Все остальные изображения в этой категории являются секционированными.
|RedHat | RHEL | 7-RAW | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.6.2019102813) | Эти образы являются неструктурированными (т. е. логические тома не были добавлены).
|RedHat | RHEL | 7-RAW-CI | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.6.2019072418) | Эти образы являются неструктурированными (т. е. логические тома не были добавлены) и будут использовать Cloud-init для подготовки.
|RedHat | RHEL | 7-LVM | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.6.2019062414) | Эти образы LVM секционированы.
|RedHat | RHEL — BYOS | RHEL-{LVM, RAW} | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.7.20190819) | Эти образы представляют собой образы RHEL 7 BYOS. Они не подключены к репозиториям и не будут взимать плату за RHEL Premium. Если вы заинтересованы в образах RHEL BYOS, [запросите доступ](https://aka.ms/rhel-byos). Значения номера SKU заканчиваются дополнительной версией и обозначают, является ли образ необработанным или LVMным. Например, значение номера SKU RHEL-lvm77 указывает на образ LVM секционирования RHEL 7,7.
|RedHat | RHEL | RHEL-SAP | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.6.2019071300) | Эти образы RHEL для образов SAP. Они имеют право доступа к репозиториям SAP HANA и приложений, а также к репозиториям E4S RHEL. Выставление счетов включает в себя RHEL Premium и SAP Premium поверх базовой стоимости вычислений.
|RedHat | RHEL | RHEL-SAP-HA | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.6.2019062320) | Эти образы RHEL для SAP с высоким уровнем доступности и образами служб обновления. Они имеют право доступа к репозиториям SAP HANA и приложений, а также к репозиториям с высоким уровнем доступности и репозиториям E4S RHEL. Выставление счетов включает в себя RHEL Premium, SAP Premium и HA Premium поверх базовой стоимости вычислений.
|RedHat | RHEL | RHEL-HA | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.6.2019062019) | Это RHEL образы, которые также имеют право на доступ к надстройке высокого уровня доступности. Они будут взиматься немного поверх RHEL и базовой стоимости вычислений из-за дополнительного уровня доступности Premium.
|RedHat | RHEL | RHEL-SAP-APPS | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.3.2017053118) | Эти образы устарели, так как приложения SAP и репозитории SAP HANA объединены в репозитории SAP. Это RHEL для образов приложений SAP. Они имеют право доступа к репозиториям приложений SAP, а также к базовым репозиториям RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 7.3.2018051421) | Эти образы устарели, так как приложения SAP и репозитории SAP HANA объединены в репозитории SAP. Это RHEL for SAP HANA образы. Они имеют право доступа к SAP HANA репозиториям, а также к базовым репозиториям RHEL.

### <a name="rhel-8-image-types"></a>Типы изображений RHEL 8
Ниже приведены сведения для типов образов RHEL 8.

|Издатель | Предложение | Значение номера SKU | Версия | Сведения
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 8.0.20191023) | Эти образы представляют собой RHEL 8,0 с LVM секционированными изображениями, подключенными к стандартным репозиториям Red Hat.
|RedHat | RHEL | 8 — Gen2 | Сцепленные значения дополнительной версии RHEL и опубликованной даты (например, 8.0.20191024) | Эти образы представляют собой LVM секционированные образы Hyper-V поколения 2 RHEL 8,0, подключенные к стандартным репозиториям Red Hat. Дополнительные сведения о виртуальных машинах поколения 2 в Azure можно найти [здесь](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="extended-update-support-eus"></a>Поддержка расширенных обновлений (ЕУС)
По состоянию на апрель 2019 доступны образы RHEL, которые подключены к репозиториям поддержки расширенных обновлений (ЕУС) по умолчанию. Дополнительные сведения о RHEL ЕУС доступны в [документации по Red Hat](https://access.redhat.com/articles/rhel-eus).

Переключение на репозитории ЕУС возможно и поддерживается. Инструкции о том, как переключить виртуальную машину на ЕУС и дополнительные сведения о сроках поддержки ЕУС, доступны [здесь](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> ЕУС не поддерживается в Дополнениех RHEL. Это означает, что если вы устанавливаете пакет, который обычно доступен в канале RHEL, вы не сможете сделать это на ЕУС. Дополнительные сведения о жизненном цикле продукта Red Hat см. [здесь](https://access.redhat.com/support/policy/updates/extras/).

### <a name="differentiating-between-regular-and-eus-images"></a>Различие между обычными и ЕУС образами.
Клиенты, которые хотят использовать образы, подключенные к репозиториям ЕУС, должны использовать образ RHEL, содержащий дополнительный номер версии RHEL в номере SKU.

Например, вы можете увидеть два следующих образа RHEL 7,4:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
В этом случае `RedHat:RHEL:7.6:7.6.2019102813` будет присоединен к репозиториям ЕУС по умолчанию (значение SKU 7,4), а `RedHat:RHEL:7-LVM:7.6.2019062414` будет подключен к репозиториям, отличным от ЕУС, по умолчанию (значение SKU, равное 7-LVM).

Если вы хотите использовать обычные репозитории (не ЕУС), разверните его с помощью образа, который не содержит дополнительный номер версии в номере SKU.

#### <a name="rhel-images-with-eus"></a>RHEL образы с помощью ЕУС
Следующая таблица будет применяться к образам RHEL, подключенным к репозиториям ЕУС.

>[!NOTE]
> На момент написания статьи только RHEL 7,4 и более поздние версии имеют поддержку ЕУС. ЕУС больше не поддерживается для RHEL < = 7,3.
>
> Дополнительные сведения о доступности RHEL ЕУС можно найти [здесь](https://access.redhat.com/support/policy/updates/errata).

Дополнительный номер версии |Пример образа ЕУС              |Состояние ЕУС                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7.4:7.4.2019041718 | Образы, опубликованные 2019 апреля и более поздних версий, будут ЕУС по умолчанию|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Образы, опубликованные 2019 июня и более поздних версий, будут ЕУС по умолчанию |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | Опубликованные образы 2019 и более поздних версий будут ЕУС по умолчанию  |
RHEL 8,0      |Н/Д                            | Нет ЕУС, доступных с Red Hat                               |





### <a name="other-available-offers-and-skus"></a>Другие доступные предложения и номера SKU
Полный список доступных предложений и номеров SKU может включать дополнительные образы других типов, не указанные в приведенной выше таблице, например `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Эти предложения могут использоваться для предоставления поддержки по конкретным решениям marketplace или могут быть опубликованы для предварительного просмотра и тестирования. Они могут быть изменены или удалены в любое время без предупреждения. Не используйте эти образы, если их наличие не было публично задокументировано корпорацией Майкрософт или Red Hat.

## <a name="publishing-policy"></a>Политика публикации
Корпорация Майкрософт и Red Hat обновляют образы по мере выпуска новых вспомогательных версий, при необходимости для решения конкретных CVE или в рамках периодических изменений и обновлений конфигурации. Мы стремимся предоставлять обновленные образы как можно скорее — в течение трех рабочих дней после выхода выпуска или исправления CVE.

Мы обновим только текущую вспомогательную версию в каждом семействе образов. С выходом новой вспомогательной версии мы перестанем обновлять старые вспомогательные версии. Например, с выходом RHEL 7.6 мы больше не будем обновлять образы RHEL 7.5.

>[!NOTE]
> Активные виртуальные машины Azure, подготовленные на основе образов RHEL с оплатой по мере использования, подключаются к Azure RHUI и могут получать обновления и исправления по мере того, как они выпускаются Red Hat и реплицируются в Azure RHUI (обычно не позднее чем через 24 часа после официального выпуска Red Hat). Для получения обновлений для этих виртуальных машин не требуется новый опубликованный образ, и клиенты получают полный контроль над установкой обновления.

## <a name="image-retention-policy"></a>Политика хранения образов
Наша текущая политика предполагает хранение всех ранее опубликованных образов. Мы оставляем за собой право удалить образы, которые вызывают какие-либо проблемы. Например, могут быть удалены образы с неверной конфигурацией из-за последующих обновлений платформы или компонентов. Для образов, которые могут быть удалены, применяется текущая политика Marketplace, которая подразумевает предоставление обновлений за 30 дней до удаления образа.

## <a name="next-steps"></a>Дальнейшие действия
* Просмотрите полный список [образов RHEL в Azure](./redhat-imagelist.md).
* Дополнительные сведения об Azure Red Hat Update Infrastructure см. [здесь](https://aka.ms/rhui-update).
* Дополнительные сведения о [предложении RHEL BYOS](./redhat-byos.md).
* Сведения о политиках поддержки Red Hat для всех версий RHEL можно найти на странице [о жизненных циклах выпусков Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
