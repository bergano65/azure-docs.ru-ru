---
title: Настройка исходного окружения для репликации из VMware в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описано, как настроить локальное окружение для репликации виртуальных машин VMware в Azure с помощью Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 536b7ed21e7dd54fcbea97951330b08925961713
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884971"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Настройка исходного окружения для репликации из VMware в Azure

В этой статье описано, как настроить исходное локальное окружение для репликации виртуальных машин VMware в Azure. В ней указаны шаги для выбора сценария репликации, настройки локального компьютера в качестве сервера конфигурации Azure Site Recovery и автоматического обнаружения локальных виртуальных машин. 

## <a name="prerequisites"></a>Технические условия

В этой статье предполагается, что вы:

- Создали план развертывания с помощью [Планировщика развертывания Azure Site Recovery](site-recovery-deployment-planner.md). Это позволяет выделить достаточную пропускную способность с учетом объема ежедневно изменяемых данных, чтобы достичь требуемого значения целевой точки восстановления (RPO).
- [настроили ресурсы](tutorial-prepare-azure.md) на [портале Azure](https://portal.azure.com);
- [настроили локальную среду VMware](vmware-azure-tutorial-prepare-on-premises.md), в том числе выделенную учетную запись для автоматического обнаружения.

## <a name="choose-your-protection-goals"></a>Выбор целевых объектов для защиты

1. В колонке **Хранилища служб восстановления** выберите имя хранилища. В этом сценарии мы используем имя **ContosoVMVault**.
2. В разделе **Приступая к работе** выберите Site Recovery. Затем выберите **Подготовка инфраструктуры**.
3. Выберите **Protection goal** (Цель защиты)  > **Where are your machines located** (Где находятся компьютеры?), а затем — **On-premises** (Локально).
4. В разделе **Куда следует реплицировать компьютеры?** выберите **To Azure** (В Azure).
5. В разделе **Are your machines virtualized** (Ваши компьютеры виртуализированы?) выберите **Yes, with VMware vSphere Hypervisor** (Да, с помощью гипервизора VMware vSphere). Нажмите кнопку **ОК**.

## <a name="set-up-the-configuration-server"></a>Настройка сервера конфигурации

Вы можете настроить сервер конфигурации в качестве локальной виртуальной машины VMware с помощью шаблона Open Virtualization Application (OVA). Ознакомьтесь с [дополнительными сведениями](concepts-vmware-to-azure-architecture.md) о компонентах, которые будут установлены на виртуальной машине VMware.

1. Изучите дополнительные сведения о [необходимых компонентах](vmware-azure-deploy-configuration-server.md#prerequisites) для развертывания сервера конфигурации.
2. [Проверьте значения емкости](vmware-azure-deploy-configuration-server.md#capacity-planning) для развертывания.
3. [Скачайте](vmware-azure-deploy-configuration-server.md#download-the-template) и [импортируйте](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) шаблон OVA для настройки локальной виртуальной машины VMware, работающей на сервере конфигурации. Лицензии, с помощью шаблона — это лицензия на пробное использование и действителен в течение 180 дней. После этого периода, пользователь должен активировать windows с помощью приобретенных лицензий.
4. Включите виртуальную машину VMware и [зарегистрируйте ее](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) в хранилище служб восстановления.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Исключение папок Azure Site Recovery из антивирусной программы

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Антивирусное программное обеспечение работает на исходном компьютере

Если антивирусное программное обеспечение работает на исходном компьютере, исключите из него папку установки. Исключение папки *C:\ProgramData\ASR\agent* позволяет сделать репликацию более плавной.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Антивирусное программное обеспечение работает на сервере конфигурации

Исключите следующие папки из антивирусного программного обеспечения, чтобы сделать репликацию более плавной и избежать проблем с подключением:

- C:\Program Files\Microsoft Azure Recover Services Agent.
- C:\Program Files\Microsoft Azure Site Recovery Provider.
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager. 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool. 
  - C:\thirdparty.
  - C:\Temp.
  - C:\strawberry.
  - C:\ProgramData\MySQL.
  - C:\Program Files (x86)\MySQL.
  - C:\ProgramData\ASR.
  - C:\ProgramData\Microsoft Azure Site Recovery.
  - C:\ProgramData\ASRLogs.
  - C:\ProgramData\ASRSetupLogs.
  - C:\ProgramData\LogUploadServiceLogs.
  - C:\inetpub.
  - Каталог установки сервера ASR. Например:  E:\Program Files (x86) \Microsoft Azure Site Recovery.

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Антивирусное программное обеспечение работает на горизонтально масштабируемом сервере процессов или главном целевом объекте

Исключите из антивирусного программного обеспечения следующие папки:

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR.
3. C:\ProgramData\ASRLogs.
4. C:\ProgramData\ASRSetupLogs.
5. C:\ProgramData\LogUploadServiceLogs.
6. C:\ProgramData\Microsoft Azure Site Recovery.
7. Каталог установки сервера ASR с балансировкой нагрузки, например C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Дальнейшие действия
[Настройка целевой среды](./vmware-azure-set-up-target.md) 
