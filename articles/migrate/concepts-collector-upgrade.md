---
title: Обновление модуля сборщика в службе "Миграция Azure" | Документация Майкрософт
description: В этой статье приводятся сведения об обновлении модуля "Сборщик Миграции Azure".
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: cd48b824845a0195fc78814a88dd449507c99394
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241469"
---
# <a name="collector-update-release-history"></a>Журнал выпуска версий обновлений Сборщика

В этой статье приведены сведения об обновлении модуля сборщика в службе [Миграция Azure](migrate-overview.md).

Сборщик Миграции Azure — это простой модуль, который можно использовать для обнаружения локального окружения vCenter для оценки перед миграцией в Azure. [Узнайте больше](concepts-collector.md).


## <a name="one-time-discovery-upgrade-versions"></a>Однократное обнаружение: версии обновления

### <a name="version-10916-released-on-10292018"></a>Версия 1.0.9.16 (выпущена 29.10.2018)

Содержит исправления ошибок PowerCLI, которые возникают при настройке устройства. 

Значения хэша для [пакета обновления 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Алгоритм** | **Значение хэша**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>Версия 1.0.9.14

Значения хэша для обновления [пакета 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Алгоритм** | **Значение хэша**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>Версия 1.0.9.13

Значения хэша для обновления [пакета 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Алгоритм** | **Значение хэша**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

### <a name="version-10911"></a>Версия 1.0.9.11

Хэш-значения для обновления [пакета 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Алгоритм** | **Значение хэша**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

### <a name="version-1097"></a>Версия 1.0.9.7

Хэш-значения для обновления [пакета 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Алгоритм** | **Значение хэша**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="continuous-discovery-upgrade-versions"></a>Непрерывное обнаружение: версии обновления

Обновление для модуля непрерывного обнаружения пока недоступно.

## <a name="run-an-upgrade"></a>Выполнение обновления

Сборщик можно обновить до последней версии, не скачивая OVA-файл повторно.

1. Скачайте пакет обновления последней версии из списка ниже.
2. Чтобы обеспечить защиту скачиваемых исправлений, откройте командное окно от имени администратора и выполните указанную ниже команду для создания хэша ZIP-файла. Созданный хэш должен совпадать с хэшем, предусмотренным для определенной версии:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Пример: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Скопируйте ZIP-файл на виртуальную машину с модулем сборщика.
4. Щелкните ZIP-файл правой кнопкой мыши и выберите **Извлечь все**.
5. Щелкните правой кнопкой мыши **Setup.ps1** > **Выполнить с помощью PowerShell** и следуйте инструкциям по установке.


## <a name="next-steps"></a>Дополнительная информация

- [Дополнительные сведения](concepts-collector.md) о модуле сборщика.
- [Выполните оценку](tutorial-assessment-vmware.md) виртуальных машин VMware.
