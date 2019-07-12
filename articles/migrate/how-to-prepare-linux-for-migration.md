---
title: Подготовка виртуальных машин VMware под управлением Linux для миграции в Azure с помощью миграции сервера миграции Azure | Документация Майкрософт
description: Описывается Подготовка виртуальной Машины Linux для миграции в Azure с помощью миграции сервера миграции Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811795"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Подготовка виртуальных машин Linux VMware для миграции в Azure 

В этой статье описывается способ подготовки виртуальных машин VMware под управлением Linux, если вы хотите перенести их в Azure с помощью ["Миграция Azure"](migrate-overview.md). 

> [!NOTE]
> Сейчас Azure перенести миграции сервера — в общедоступной предварительной версии. Обнаружение и оценка виртуальных машин для миграции можно использовать существующую версию GA "Миграция Azure", но фактический перенос не поддерживается в существующих Общедоступные версии.

Подготовьте компьютеры Linux следующим образом.

1. Установите службы интеграции Linux Hyper-V. Более новые версии дистрибутивов Linux, возможно, он установлен по умолчанию).
2. Перестройте init образ Linux, чтобы он содержал необходимые драйверы Hyper-V, и таким образом, чтобы виртуальная машина загрузится в Azure (требуется для некоторых дистрибутивов).
3. Включите ведение журнала последовательной консоли для устранения неполадок. [Узнайте больше](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console).
4. Обновите файл сопоставления устройств с именем устройства для сопоставления тома, для использования идентификаторов постоянных устройств.
5. Обновите записи fstab для использования идентификаторов постоянного тома.
6. Удалите все правила udev, Резервировать имена интерфейсов на основе MAC-адреса и т.д.
7. Обновление сетевых интерфейсов для получения IP-адреса DHCP.
8. Обеспечить ssh включен. Проверьте, что службы sshd настроен автоматический запуск при перезагрузке.
9. Убедитесь, что входящие ssh запросов на подключение не блокируются брандмауэром операционной системы или правил таблицы IP-адресов.

[Дополнительные сведения](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) о внесении этих изменений для самых популярных дистрибутивов Linux.

## <a name="sample-script"></a>Пример скрипта

Этот сценарий (Подготовка для azure.sh) представлен пример для подготовки машин Linux. Сценарий может не работать для всех дистрибутивов и сред, но это удобную отправную точку.

В сценария показано как: 

- Повторное создание образа init Linux с помощью необходимые драйверы при необходимости.
- Обновите записи fstab для использования идентификаторов постоянного тома.
- Перенаправление журналов консоли на последовательный порт.
- Включить консоль Azure последовательного доступа
- Удалить net правила udev
- Внедрить запуск загрузочный сценарий, который выполняется при загрузке виртуальной Машины. Он проверяет, если виртуальная машина работает в Azure. Если это так, он обновляет конфигурацию сети на виртуальной Машине и устанавливает первый интерфейс ethernet на использование DHCP для получения IP-адресом.

### <a name="before-you-start"></a>Перед началом работы

- Пример сценария содержит примеры действий. Он не должен выполняться в рабочих системах. Он может привести к повреждению или повредить виртуальной Машиной, на котором он работает.
- Мы рекомендуем запустить его на тестовой виртуальной Машины. Прежде чем начать, резервное копирование виртуальной Машины или моментального снимка можно воспользоваться таким образом, чтобы при необходимости можно восстановить виртуальную Машину. 
- Сценарий работает, если виртуальная машина работает под управлением одной из следующих дистрибутивов Linux:
    - Red Hat Enterprise Linux 6.5 + 7.1 +
    - Cent OS 6.5+, 7.1+
    - SUSE Linux Enterprise Server 12 SP1,SP2, SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>Запуск сценария

1. Копировать скрипт в файл Linux тестов виртуальной Машины с помощью sftp или клиент scp, например FileZilla или WinScp.
2. SSH к компьютеру Linux, используя учетную запись администратора.
3. Перейдите в каталог скрипта.
4. Чтобы сценарий в исполняемый файл, запустите **sudo chmod 777 Подготовка для azure.sh**.
5. Запустите скрипт с **./prepare-for-azure.sh**.

Ниже приведен его выполнения.

![Скрипт Linux](./media/how-to-prepare-linux-for-migration/script1.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script2.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script3.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script4.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script5.png)
![скрипта Linux ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Сценария Linux](./media/how-to-prepare-linux-for-migration/script7.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script8.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script9.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script10.png)
![скрипта Linux ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Сценария Linux](./media/how-to-prepare-linux-for-migration/script12.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script13.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script14.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script15.png)
![скрипта Linux ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Сценария Linux](./media/how-to-prepare-linux-for-migration/script17.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script18.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script19.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script20.png)
![скрипта Linux ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Сценария Linux](./media/how-to-prepare-linux-for-migration/script22.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script23.png)
![сценария Linux](./media/how-to-prepare-linux-for-migration/script24.png)
![скрипта Linux](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>Следующие шаги

- Узнайте, как использовать [сопоставление зависимостей компьютеров](how-to-create-group-machine-dependencies.md), чтобы создать группы с высокой достоверностью.
- [Узнайте больше](concepts-assessment-calculation.md) о вычислении оценок.
