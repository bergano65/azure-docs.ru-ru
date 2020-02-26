---
title: Подключение Azure Stack виртуальных машин к Azure Sentinel | Документация Майкрософт
description: В этой статье показано, как подготавливать расширение виртуальной машины Azure Monitor, Update и Configuration Management на Azure Stack виртуальных машинах и приступить к их мониторингу с помощью Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588524"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Подключение Azure Stack виртуальных машин к Azure Sentinel




С помощью Sentinel Azure можно отслеживать виртуальные машины, работающие в Azure, и Azure Stack в одном месте. Чтобы подключить Azure Stack машины к Azure Sentinel, сначала необходимо добавить расширение виртуальной машины к существующим Azure Stackным виртуальным машинам. 

После подключения Azure Stack машины выберите из коллекции панели мониторинга, в которой отображаются сведения на основе данных. Эти панели мониторинга можно легко настроить в своих нуждах.



## <a name="add-the-virtual-machine-extension"></a>Добавление расширения виртуальной машины 

Добавьте расширение виртуальной машины **Azure Monitor, Update и Configuration Management** в виртуальные машины, работающие на Azure Stack. 

1. На новой вкладке браузера Войдите на [портал Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Перейдите на страницу **виртуальные машины** , выберите виртуальную машину, которую необходимо защитить с помощью Azure Sentinel. Сведения о том, как создать виртуальную машину на Azure Stack, см. в статье Создание виртуальной машины [Windows Server с помощью портала Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) или [Создание виртуальной машины сервера Linux с помощью портала Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Выберите **Расширения**. Появится список расширений виртуальных машин, которые установлены на этой виртуальной машине.
4. Щелкните вкладку **Добавить** . Откроется колонка меню **новый ресурс** , в которой будет показан список доступных расширений виртуальной машины. 
5. Выберите расширение **управления Azure Monitor, обновление и настройка** и нажмите кнопку **создать**. Откроется окно **Установка расширения** конфигурации.

   ![Параметры управления Azure Monitor, обновления и конфигурации](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Если вы не видите расширение **управления Azure Monitor, обновления и настройки** , указанное в Marketplace, обратитесь к оператору Azure Stack, чтобы сделать его доступным.

6. В меню Sentinel Azure выберите **параметры рабочей области** , а затем — **Дополнительно**и скопируйте **идентификатор рабочей области** и **ключ рабочей области (первичный ключ)** . 
1. В окне Azure Stack **установить расширение** вставьте их в указанные поля и нажмите кнопку **ОК**.
1. После завершения установки расширения его состояние отображается как **Подготовка выполнена**. Для отображения виртуальной машины на портале Sentinel Azure может потребоваться до одного часа.

Дополнительные сведения об установке и настройке агента для Windows см. в разделе [Подключение компьютеров Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Сведения об устранении неполадок с агентами Linux см. в статье [Устранение неполадок с агентом Log Analytics для Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

На портале Sentinel Azure в Azure в разделе **виртуальные машины**представлены общие сведения обо всех виртуальных машинах и компьютерах, а также их состояние. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Если расширение больше не нужно, можете удалить его с виртуальной машины на портале Azure Stack.

Чтобы удалить расширение, сделайте следующее:

1. Откройте **портал Azure Stack**.
2. Перейдите к странице **Виртуальные машины**, выберите виртуальную машину, с которой нужно удалить расширение.
3. Выберите **Расширения** и щелкните расширение **Microsoft.EnterpriseCloud.Monitoring**.
4. Щелкните **Удалить**и подтвердите выбор.

## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- Узнайте, как выполнять потоковую передачу из [устройств Common Event Format](connect-common-event-format.md) в Azure Sentinel.
