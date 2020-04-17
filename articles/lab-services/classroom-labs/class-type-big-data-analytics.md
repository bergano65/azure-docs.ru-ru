---
title: Настройка лаборатории для обучения аналитике больших данных с помощью лабораторных служб Azure (ru) Документы Майкрософт
description: Узнайте, как создать лабораторию для обучения аналитике больших данных с помощью развертывания Docker платформы данных Hortonworks (HDP).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538782"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Настройка лаборатории для анализа больших данных с помощью развертывания Docker платформы данных HortonWorks

В этой статье показано, как создать лабораторию для обучения классу аналитики больших данных.  С помощью этого типа класса студенты учатся обрабатывать большие объемы данных и применять алгоритмы машинного и статистического обучения для получения информации.  Ключевой целью для студентов является научиться использовать инструменты анализа данных, такие как [пакет программного обеспечения Apache Hadoop](https://hadoop.apache.org/) с открытым исходным кодом, который предоставляет инструменты для хранения, управления и обработки больших данных.

В этой лаборатории студенты будут использовать популярную коммерческую версию Hadoop, предоставленную [Cloudera](https://www.cloudera.com/), под названием [Hortonworks Data Platform (HDP).](https://www.cloudera.com/products/hdp.html)  В частности, студенты будут использовать [HDP Sandbox 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) который является упрощенной, простой в использовании версии платформы, которая является бесплатной и предназначена для обучения и экспериментов.  Хотя этот класс может использовать либо Windows или Linux виртуальные машины (VM) с HDP Песочница развернуты, эта статья покажет, как использовать Windows.

Еще один интересный аспект этой лаборатории, является то, что мы будем развертывать HDP Песочница на лабораторных VMs с использованием [контейнеров Docker.](https://www.docker.com/)  Каждый контейнер Docker предоставляет свою изолированную среду для программных приложений для работы внутри.  Концептуально контейнеры Docker подобны вложенным ВМ и могут быть использованы для легкого развертывания и запуска широкого спектра программных приложений на основе изображений контейнеров, предоставленных на [Docker Hub.](https://www.docker.com/products/docker-hub)  Скрипт развертывания Cloudera для HDP Sandbox автоматически вытягивает [изображение HDP Sandbox 3.0.1 Docker](https://hub.docker.com/r/hortonworks/sandbox-hdp) из Docker Hub и запускает два контейнера Docker:
  - песочница-hdp
  - песочница-прокси

## <a name="lab-configuration"></a>Настройка лаборатории

Чтобы настроить эту лабораторию, вам нужна подписка Azure и лабораторный учет, чтобы начать работу. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу. Получив подписку Azure, можно создать новую учетную запись лаборатории в лабораторных службах Azure. Для получения дополнительной информации о [Tutorial to Setup a Lab Account](tutorial-setup-lab-account.md)создании новой учетной записи лаборатории см.  Вы также можете использовать существующую учетную запись лаборатории.

### <a name="lab-account-settings"></a>Настройки учетной записи лаборатории

Включить параметры, описанные в таблице ниже, для лабораторной учетной записи. Для получения дополнительной информации о том, как включить изображения на рынке, [см.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

| Настройка учетной записи лаборатории | Instructions |
| ------------------- | ------------ |
|Изображение рынка| Включите изображение Windows 10 Pro для использования в вашей учетной записи лаборатории.|

### <a name="lab-settings"></a>Настройки лаборатории

Используйте настройки в таблице ниже при настройке классной лаборатории.  Для получения дополнительной информации о [set up a classroom lab tutorial](tutorial-setup-classroom-lab.md)том, как создать классную лабораторию, см.

| Настройки лаборатории | Значение/инструкции |
| ------------ | ------------------ |
|Размер виртуальной машины| Средний (Nested Виртуализация). Этот размер VM лучше всего подходит для реляционных баз данных, кэширования в памяти и аналитики.  Этот размер также поддерживает вложенную виртуализацию.|  
|Образ виртуальной машины| Windows 10 Профессиональная|

> [!NOTE] 
> Мы должны использовать Medium (Nested Виртуализация), так как развертывание HDP Sandbox с помощью Docker требует:
>   - Windows Hyper-V с вложенной виртуализацией
>   - Не менее 10 ГБ оперативной памяти

## <a name="template-machine-configuration"></a>Конфигурация машины шаблона

Для настройки шаблона машины, мы будем:
- Установка Docker
- Развертывание песочницы HDP
- Используйте PowerShell и Windows Task Err для автоматического запуска контейнеров Docker

### <a name="install-docker"></a>Установка Docker

Шаги в этом разделе основаны на [инструкциях Cloudera для развертывания с контейнерами Docker.](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html) 

Чтобы использовать контейнеры Docker, необходимо сначала установить Docker Desktop на шаблон VM:

1. Выполните действия в [разделе Prerequisites](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) для установки [Docker для Windows.](https://docs.docker.com/docker-for-windows/install/) 

    > [!IMPORTANT] 
    > Убедитесь, что **контейнеры use Windows вместо конфигурации контейнеров Linux** не будут проверены.

1. Убедитесь, что **Windows контейнеры и функции Hyper-V** включены.
   ![Включение и отключение компонентов Windows](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Выполните последующие действия в разделе [«Память для Windows»,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) чтобы настроить конфигурацию памяти Докера.

    > [!WARNING]
    > Если вы случайно проверите **контейнеры Use Windows вместо** опции контейнеров Linux при установке Docker, вы не увидите настройки конфигурации памяти.  Чтобы исправить это, вы можете переключиться на использование linux контейнеров, [нажав на значок Docker в лоток Windows System;](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) когда меню Docker Desktop **откроется, выберите перейти на linux контейнеры.**
 
### <a name="deploy-hdp-sandbox"></a>Развертывание песочницы HDP

В этом разделе вы развернете hdP Sandbox, а затем также получите доступ к hdP Sandbox с помощью браузера.

1. Убедитесь, что вы установили [Git Bash,](https://gitforwindows.org/) как указано в [разделе Prerequisites](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) руководства, так как это рекомендуется для завершения следующих шагов.

1. Используя [руководство по развертыванию и установке Cloudera для Docker,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)выполните следующие этапы:
   
   -    Развертывание песочницы HDP
   -    Проверить HDP песочница

    > [!WARNING] 
    > При загрузке последнего файла .zip для HDP убедитесь, что вы *не* сохраните файл .zip в пути каталога, который включает в себя пробел.

    > [!NOTE] 
    > Если во время развертывания вы получаете исключение, в котором указывается, что **Диск не был общим,** вам необходимо поделиться своим C-диском с Docker, чтобы контейнеры HDP Linux могли получить доступ к локальным файлам Windows.  Чтобы исправить это, [нажмите на значок Docker в лоток Windows System,](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) чтобы открыть меню Docker Desktop и выберите **настройки.**  При открытии диалога о **настройках Docker** выберите **ресурсы > обмена файлами** и проверьте **диск C.**  Затем можно повторить шаги по развертыванию HDP Sandbox.

1. После развертывания и запуска контейнеров Docker для HDP Sandbox вы можете получить доступ к среде, запустив браузер и следуя инструкциям Cloudera для открытия страницы Welcome [Sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) и запуска dashboard HDP.

    > [!NOTE] 
    > Эти инструкции предполагают, что вы сначала нанесли на карту локальный IP-адрес среды песочницы sandbox-hdp.hortonworks.com в файле хоста на вашем шаблоне VM.  Если вы **не** сделаете это отображение, вы можете получить [http://localhost:8080](http://localhost:8080)доступ к странице Sandbox Добро пожаловать, навигация по .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Автоматически запускайте контейнеры Docker при входе студентов в систему

Чтобы обеспечить простой в использовании опыт для студентов, мы будем использовать сценарий PowerShell, который автоматически:
  - Запускает контейнеры HDP Sandbox Docker, когда студент начинает и подключается к их лаборатории VM.
  - Запускает браузер и переходит на страницу добро пожаловать в песочницу.
Мы также будем использовать Windows Task Scheduler для автоматического запуска этого скрипта, когда студент входит в свой VM.
Чтобы настроить это, выполните следующие действия: [big Data Analytics скриптования](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Калькуляции

Если вы хотите оценить стоимость этой лаборатории, вы можете использовать следующий пример.

Для класса из 25 студентов с 20 часами запланированного времени занятий и 10 часами квоты для домашних заданий или заданий, цена для лаборатории будет:
  - 25 студентов (20 и 10) часов 55 лабораторных единиц 0,01 USD в час

Более подробную информацию о ценах можно узнать о ценах на [лабораторную политику Azure Lab.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Заключение

В этой статье вы прошли через шаги, необходимые для создания лаборатории для класса анализа больших данных, которая использует платформу данных Hortonworks, развернутую с Docker.  Настройка для этого типа может быть использована для аналогичных классов анализа данных.  Эта настройка может также применяться к другим типам классов, которые используют Docker для развертывания.

## <a name="next-steps"></a>Следующие шаги

Следующие шаги являются общими для создания любой лаборатории.

- [Создание и управление шаблоном](how-to-create-manage-template.md)
- [Добавление пользователей](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Установленная квота](how-to-configure-student-usage.md#set-quotas-for-users)
- [Установить расписание](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Ссылки на регистрацию по электронной почте студентам](how-to-configure-student-usage.md#send-invitations-to-users)
