---
title: На борту виртуальных машин Azure Stack в Azure Sentinel Документы Майкрософт
description: В этой статье показано, как обеспечить расширение виртуального управления azure Monitor, Update и Configuration Management на виртуальных машинах Azure Stack и начать мониторинг их с помощью Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588524"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Подключите виртуальные машины Azure Stack к Azure Sentinel




С помощью Azure Sentinel вы можете отслеживать свои вм- управления, работающие на Azure и Azure Stack в одном месте. Чтобы на борт машин Azure Stack перейти в Azure Sentinel, сначала необходимо добавить расширение виртуальной машины к существующим виртуальным машинам Azure Stack. 

После подключения машин Azure Stack выберите галерею панелей мониторинга, которые на основе данных посевные данные. Эти панели мониторинга могут быть легко настроены под ваши потребности.



## <a name="add-the-virtual-machine-extension"></a>Добавить расширение виртуальной машины 

Добавьте расширение виртуального автомата **Azure Monitor, Update and Configuration Management** в виртуальные машины, работающие на вашем Azure Stack. 

1. Во введении в новую вкладку браузера войдите на [портал Azure Stack.](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)
2. Перейдите на страницу **виртуальных машин,** выберите виртуальную машину, которую вы хотите защитить с помощью Azure Sentinel. Для получения информации о том, как создать виртуальную машину на Azure Stack, [см. Создать сервер Windows VM с порталом Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) или создать [Linux-сервер VM с помощью портала Azure Stack.](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)
3. Выберите **расширения.** Появится список расширений виртуальных машин, которые установлены на этой виртуальной машине.
4. Нажмите на вкладку **Добавить.** Лопасти меню **«Новый ресурс»** открывается и показывает список доступных виртуальных расширений машины. 
5. Выберите расширение **Azure Monitor, обновление и управление конфигурацией** и нажмите **«Создать».** Открывается окно конфигурации **расширения Установки.**

   ![Настройки монитора, обновления и управления конфигурацией Azure](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Если вы не видите расширение **Azure Monitor, Update and Configuration Management,** указанное на вашем рынке, обратитесь к оператору Azure Stack, чтобы сделать его доступным.

6. В меню Azure Sentinel выберите **параметры рабочего пространства,** за которыми следует **Advanced,** и скопируйте **идентификатор рабочего пространства** и **ключ рабочего пространства (Primary Key)**. 
1. В окне расширения Azure Stack **Установить** их вуказанные поля и нажмите **OK**.
1. После завершения установки расширения ее статус отображается как **Подготовка Успешно.** Появление виртуальной машины на портале Azure Sentinel может занять до одного часа.

Для получения дополнительной информации об установке и настройке агента для Windows [см.](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)

Сведения об устранении неполадок с агентами Linux см. в статье [Устранение неполадок с агентом Log Analytics для Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

На портале Azure Sentinel на Azure, под **виртуальными машинами,** у вас есть обзор всех виртуальных технологий и компьютеров, а также их статус. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Если расширение больше не нужно, можете удалить его с виртуальной машины на портале Azure Stack.

Чтобы удалить расширение, сделайте следующее:

1. Откройте **портал Azure Stack**.
2. Перейдите к странице **Виртуальные машины**, выберите виртуальную машину, с которой нужно удалить расширение.
3. Выберите **Расширения** и щелкните расширение **Microsoft.EnterpriseCloud.Monitoring**.
4. Нажмите на **Uninstall**и подтвердите свой выбор.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- Узнайте, как выполнять потоковую передачу из [устройств Common Event Format](connect-common-event-format.md) в Azure Sentinel.
