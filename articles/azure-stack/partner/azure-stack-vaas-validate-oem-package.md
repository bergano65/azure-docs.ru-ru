---
title: Проверка пакетов поставщика вычислительной техники (OEM) с помощью проверки как услуги Azure Stack | Документация Майкрософт
description: Узнайте, как проверить пакеты поставщика вычислительной техники (OEM) с помощью проверки как услуги.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 5ba290f442f4c27b510538d7c1f7b5e27467efc5
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246659"
---
# <a name="validate-oem-packages"></a>Проверка пакетов OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Вы можете протестировать новый пакет OEM при изменение встроенного ПО или драйверов для проверки завершенного решения. Пакет подписывается корпорацией Майкрософт после прохождения теста. В проверке должен содержаться обновленный пакет расширений OEM с драйверами и встроенным ПО, которые прошли проверки по логотипу и PCS Windows Server.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Перед передачей или отправкой пакетов ознакомьтесь со статьей [Create an OEM package](azure-stack-vaas-create-oem-package.md) (Создание пакета OEM), чтобы получить сведения о предполагаемом формате и содержимом пакета.

## <a name="managing-packages-for-validation"></a>Управление пакетами для проверки

При использовании рабочего процесса **проверки пакета**, чтобы проверить пакет, необходимо будет указать URL-адрес **Azure Storage Blob**. Этот большой двоичный объект является пакетом OEM, установленным в решении во время развертывания. Для создания большого двоичного объекта с помощью учетной записи хранения Azure, созданной во время установки, обратитесь к статье [Руководство по настройке ресурсов для проверки как услуги](azure-stack-vaas-set-up-resources.md).

### <a name="prerequisite-provision-a-storage-container"></a>Предварительные требования: подготовка контейнера хранилища

Создайте контейнер в учетной записи хранения для больших двоичных объектов пакета. Этот контейнер может использоваться для всех ваших запусков проверки пакета.

1. На [портале Azure](https://portal.azure.com) перейдите к учетной записи хранения, созданной в рамках статьи [Руководство по настройке ресурсов для проверки как услуги](azure-stack-vaas-set-up-resources.md).
2. В левой колонке в разделе **Служба BLOB-объектов** выберите **Контейнеры**.
3. Выберите **+ Container** (+ Контейнер) в строке меню и укажите имя для контейнера, например `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Отправка пакета в учетную запись хранения

1. Подготовьте пакет, который требуется проверить. Если пакет содержит несколько файлов, сожмите их в файл `.zip`.
2. На [портале Azure](https://portal.azure.com) выберите контейнер пакета и отправьте его, выбрав в строке меню **Отправить**.
3. Выберите файл `.zip` пакета для отправки. Оставьте значения по умолчанию для **типа большого двоичного объекта** (т. е. **блочный BLOB-объект**) и **размера блока**.

> [!NOTE]
> Убедитесь, что содержимое `.zip` помещается в корне файла `.zip`. В пакете не должно быть вложенных папок.

### <a name="generate-package-blob-url-for-vaas"></a>Создание URL-адреса большого двоичного объекта пакета для VaaS

При создании рабочего процесса **проверки пакета** на портале VaaS необходимо будет указать URL-адрес Azure Storage Blob, содержащего пакет.

#### <a name="option-1-generating-an-account-sas-url"></a>Вариант 1. Создание подписанного URL-адреса учетной записи

1. Перейдите в свою учетную запись хранения на [портале Azure](https://portal.azure.com/) и откройте ZIP-архив с вашим пакетом.

2. В контекстном меню выберите **Generate SAS** (Создать SAS).

3. Выберите **Read** (Чтение) в списке **разрешений**.

4. Задайте текущее время в поле **Start time** (Время начала), а в поле **End time** (Время окончания) — время, отстоящее минимум на 48 часов от значения **Start time** (Время начала). Если вы будете запускать другие тесты с помощью этого пакета, можно увеличить **время окончания** для выполнения тестирования. Все тесты, подготовленные к выполнению с помощью VaaS после истечения **времени окончания**, будут отклонены, и вам нужно будет повторно создать SAS.

5. Щелкните **Generate blob SAS token and URL** (Создать маркер и URL-адрес SAS большого двоичного объекта).

Используйте **URL-адрес SAS большого двоичного объекта** при запуске нового рабочего процесса **проверки пакета** на портале VaaS.

#### <a name="option-2-using-public-read-container"></a>Вариант 2. Использование контейнера с общим доступом на чтение

> [!CAUTION]
> Благодаря этому параметру контейнер открывается для анонимного доступа только для чтения.

1. Предоставьте **общий доступ на чтение только для больших двоичных объектов** к контейнеру пакета, следуя инструкциям в разделе [Предоставление анонимным пользователям разрешений для контейнеров и больших двоичных объектов](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

2. В контейнере пакета выберите большой двоичный объект пакета, чтобы открыть панель свойств.

3. Скопируйте **URL-адрес**. Используйте это значение при запуске нового рабочего процесса **проверки пакета** на портале VaaS.

## <a name="apply-monthly-update"></a>Применение ежемесячного обновления

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>Создание рабочего процесса проверки пакета

1. Войдите на [портал VaaS](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Выберите **Start** (Начать) на плитке **Package Validation** (Проверка пакета).

    ![Плитка рабочего процесса проверки пакета](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Введите URL-адрес Azure Storage Blob для пакета OEM, установленного в решении во время развертывания. Инструкции см. в разделе [Создание URL-адреса большого двоичного объекта пакета для VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Параметры среды невозможно изменить после создания рабочего процесса.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Вы будете перенаправлены на страницу сводок по тестам.

## <a name="run-package-validation-tests"></a>Запуск тестов проверки пакета

1. На странице **Package validation tests summary** (Сводки по тестам проверки пакета) вы увидите список тестов, необходимых для завершения проверки. В этом рабочем процессе тесты выполняются примерно в течение 24 часов.

    В рабочих процессах проверки при **планировании** теста используются общие параметры уровня рабочего процесса, которые вы указали во время его создания (см. раздел [Workflow common parameters for Azure Stack Validation as a Service](azure-stack-vaas-parameters.md) (Общие параметры рабочих процессов для проверки как услуги в Azure Stack)). Если какие-либо из значений параметров станут недействительными, необходимо указать их повторно, как описано в разделе об [изменении параметров рабочего процесса](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > При планировании проверочного теста через существующий экземпляр на портале будет создан новый экземпляр вместо старого. Журналы для старого экземпляра будут сохранены, но недоступны на портале.  
    После успешного завершения теста действие **Расписание** становится недоступным.

2. Выберите агент, с помощью которого будет выполняться тест. Дополнительные сведения о добавлении локальных агентов для тестирования см. в статье [Развертывание локального агента](azure-stack-vaas-local-agent.md).

3. Для каждого из следующих тестов выполните шаги 4 и 5:
    - OEM Extension Package Verification;
    - Cloud Simulation Engine.

4. В контекстном меню выберите **Расписание**, чтобы открыть командную строку для планирования выполнения тестового экземпляра.

5. Проверьте параметры теста, а затем выберите **Отправить**, чтобы запланировать выполнение теста.

Когда все тесты будут успешно выполнены, отправьте имя своей проверки пакета и решения VaaS по адресу [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) для запроса на подписание пакета.

## <a name="next-steps"></a>Дополнительная информация

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)