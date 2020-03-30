---
title: Настройка прибора Azure Migrate со скриптом
description: Узнайте, как настроить прибор Azure Migrate со сценарием
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337692"
---
# <a name="set-up-an-appliance-with-a-script"></a>Настройка прибора со скриптом

В этой статье описывается, как настроить [прибор Azure Migrate](deploy-appliance.md) с помощью скрипта установки PowerShell.

Скрипт обеспечивает:
- Альтернатива настройке прибора с использованием шаблона OVA для оценки и безагентной миграции VMw.
- Альтернатива настройке прибора с использованием шаблона VHD для оценки и миграции Hyper-VM.
- Для оценки физических серверов (или vMs, которые вы хотите перенести в качестве физических серверов), скрипт является единственным методом настройки прибора.

## <a name="prerequisites"></a>Предварительные требования

Скрипт настраивает прибор Azure Migrate на существующую физическую машину или VM.

- Машина, которая будет выступать в качестве устройства, должна работать под управлением Windows Server 2016, с 32 ГБ памяти, восемью vCPUs, около 80 ГБ дискового хранилища и внешним виртуальным коммутатором. Для этого требуется статический или динамический IP-адрес, а также доступ к Интернету.
- Перед развертыванием прибора просмотрите подробные требования к приборам [vMw,](migrate-appliance.md#appliance---vmware) [Hyper-V VMs](migrate-appliance.md#appliance---hyper-v)и [физическим серверам.](migrate-appliance.md#appliance---physical)
- Не запускайте скрипт на существующем приборе Azure Migrate.


## <a name="download-the-script"></a>Скачать сценарий

1. Найдите машину/VM, которая будет выступать в качестве прибора Azure Migrate.
2. На машине, сделать следующее:

    - Чтобы использовать прибор с VMware VMs или Hyper-V VMs, [загрузите](https://go.microsoft.com/fwlink/?linkid=2105112) папку с молнией, содержащую скрипт установки и MSIs.
    - Чтобы использовать прибор с физическими серверами, загрузите скрипт с портала Azure Migrate, как описано в этом [учебнике.](tutorial-assess-physical.md#set-up-the-appliance)

## <a name="verify-file-security"></a>Проверка безопасности файлов

Прежде чем развертывать сжатый ZIP-файл, убедитесь, что он не поврежден.

1. На компьютере, на который был скачан файл, откройте командное окно с правами администратора.
2. Выполните следующую команду, чтобы создать хэш ZIP-файла.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Пример использования: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Убедитесь, что значения генерируемого хэша соответствуют этим параметрам (для последней версии прибора):

    **Алгоритм** | **Значение хэша**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Выполнение скрипта

Вот что делает скрипт:

- Устанавливает агенты и веб-приложение.
- Устанавливает роли Windows, включая службу активации Windows, IIS и PowerShell ISE.
- Загружает и устанавливает модуль IIS, переизменяемый. Ознакомьтесь с [дополнительными сведениями](https://www.microsoft.com/download/details.aspx?id=7435).
- Обновление ключа реестра (HKLM) с постоянными настройками для Azure Migrate.
- Создает файлы журналов и конфигураций следующим образом:
    - **Файлы конфигурации**: %ProgramData%\Microsoft Azure\Config
    - **Файлы журнала**: %ProgramData%\Microsoft Azure\Logs

Выполните следующее, чтобы запустить этот сценарий.

1. Извлеките застегнутый на молнию файл в папку на машине, которая будет размещать прибор.
2. Запуск PowerShell на машине, с администратором (повышенные) привилегии.
3. Измените каталог PowerShell в папку, содержащую содержимое, извлеченное из загруженного файла на молнии.
4. Выполнить сценарий **AzureMigrateInstaller.ps1** следующим образом:

    - Для VMware: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Для Hyper-V:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - Для физических серверов:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. После успешного запуска скрипта он запускает веб-приложение для обработки приборов, чтобы можно было настроить прибор. Если вы столкнулись с какими-либо проблемами, вы можете просмотреть журналы скриптов по адресу C:'ProgramData-Microsoft Azure-AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

## <a name="next-steps"></a>Дальнейшие действия

После настройки прибора с помощью скрипта следуйте следующим инструкциям:

- Настройка прибора для [VMware.](how-to-set-up-appliance-vmware.md#configure-the-appliance)
- Настройка прибора для [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Настройка прибора для [физических серверов.](how-to-set-up-appliance-physical.md)