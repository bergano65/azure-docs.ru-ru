---
title: Хранение контейнера профиля FSLogix Windows Virtual Desktop - Azure
description: Варианты хранения профиля Windows Virtual Desktop FSLogix в Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127518"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Параметры хранения контейнеров профиля FSLogix в виртуальном рабочем столе Windows

Azure предлагает несколько решений для хранения данных, которые можно использовать для хранения контейнера профиля FSLogix. В этой статье сравниваются решения для хранения данных, которые Azure предлагает для контейнеров профиля пользователя Windows Virtual Desktop FSLogix.

Windows Virtual Desktop предлагает контейнеры для профилей FSLogix в качестве рекомендуемого решения для профиля пользователя. FSLogix предназначен для роуминга профилей в удаленных вычислительных средах, таких как Windows Virtual Desktop. При входе в систему этот контейнер динамически прикрепляется к вычислительной среде с помощью привычно поддерживаемого виртуального жесткого диска (VHD) и виртуального жесткого диска Hyper-V (VHDX). Профиль пользователя сразу же доступен и отображается в системе точно так же, как родной профиль пользователя.

В следующих таблицах сравниваются решения для хранения данных, которые Предлагает Azure Storage для профилей пользователей контейнеров для пользователей Windows Virtual Desktop FSLogix.

## <a name="azure-platform-details"></a>Детали платформы Azure

|Компоненты|Файлы Azure|Azure NetApp Files|Локальные дисковые пространства|
|--------|-----------|------------------|---------------------|
|Вариант использования|Общего назначения|Ультра производительность или миграция из NetApp на месте|Кроссплатформенный|
|Сервис платформы|Да, решение Azure- родное|Да, решение Azure- родное|Нет, самоуправляемые|
|Доступность по регионам|все регионы.|[Выбор регионов](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|все регионы.|
|Избыточность|Локально избыточно/зонально-излишний/гео-лишний|Локально избыточный|Локально избыточно/зонально-излишний/гео-лишний|
|Уровни и производительность|Standard<br>Premium<br>До максимума 100k IOPS на акцию с 5 GBps на акцию около 3 мс задержки|Standard<br>Premium<br>Ультра<br>До 320k (16K) IOPS с 4,5 GBps на объем около 1 мс задержки|Стандартная HDD: до 500 IOPS на диск овешных ограничений<br>Стандартный SSD: до 4k IOPS на диск овешных ограничений<br>Премиум SSD: до 20k IOPS на диск овешных ограничений<br>Мы рекомендуем премиум диски для хранения пространства Прямая|
|Capacity|100 TiB за акцию|100 TiB за том, до 12,5 PiB за подписку|Максимум 32 TiB на диск|
|Необходимая инфраструктура|Минимальный размер доли 1 GiB|Минимальная емкость пула 4 TiB, размер мин объем 100 GiB|Два ВМ на Azure IaaS (облачный свидетель) или по крайней мере три ВМ без и стоимость дисков|
|Протоколы|SMB 2.1/3. и REST|NFSv3, NFSv4.1 (предварительный просмотр), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Детали управления Azure

|Компоненты|Файлы Azure|Azure NetApp Files|Локальные дисковые пространства|
|--------|-----------|------------------|---------------------|
|Доступ|Облако, на месте и гибридный (синхронизация файлов Azure)|Облако, на территории (через ExpressRoute)|Облако, на территории|
|Резервное копирование|Интеграция моментального снимка резервного копирования Azure|Снимки файлов Сети Azure|Интеграция моментального снимка резервного копирования Azure|
|Безопасность и соответствие требованиям|[Все поддерживаемые Azure сертификаты](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|Завершена работа По завершении ISO|[Все поддерживаемые Azure сертификаты](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Интеграция Azure Active Directory|[Активный каталог коренных народов и службы активных доменов каталогов Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Службы домена Azure Active Directory и активный каталог native](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Поддержка домена Native Active Directory или Azure Active Directory Domain Services|

После того как вы выбрали метод хранения данных, ознакомьтесь с [ценами](https://azure.microsoft.com/pricing/details/virtual-desktop/) на Windows Virtual Desktop для получения информации о наших тарифных планах.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о контейнерах профиля FSLogix, дисках профиля пользователя и других технологиях профиля пользователя, см. таблицу в [контейнерах профиля FSLogix и файлах Azure.](fslogix-containers-azure-files.md)

Если вы готовы создать свои собственные контейнеры профиля FSLogix, начать работу с одним из этих учебников:

- [Начало работы с контейнерами профиля FSLogix в файлах Azure в виртуальном рабочем столе Windows](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Создание контейнера профиля FSLogix для пула хоста с помощью файлов Azure NetApp](create-fslogix-profile-container.md)
- Инструкции в [развертывании двухузлового сервера хранения данных Direct для хранения UPD в Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) также применяются при использовании контейнера профиля FSLogix вместо диска профиля пользователя

Вы также можете начать с самого начала и настроить собственное решение Windows Virtual Desktop в [создать арендатора в Windows Virtual Desktop.](tenant-setup-azure-active-directory.md)
