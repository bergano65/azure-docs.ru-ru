---
title: Экспорт шаблона управления ресурсами отдела ресурсов Azure Stream Analytics
description: В этой статье описывается, как экспортировать шаблон менеджера ресурсов Azure для работы в Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968935"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Экспорт шаблона управления ресурсами отдела ресурсов Azure Stream Analytics

[Шаблоны Менеджер ресурсов Azure](../azure-resource-manager/templates/overview.md) позволяют реализовать инфраструктуру в качестве кода. Шаблон представляет собой файл JavaScript Object Notation (JSON), определяющий инфраструктуру и конфигурацию ресурсов. Вы указываете ресурсы для развертывания и свойства для этих ресурсов.

Можно передислоцировать задание Azure Stream Analytics, экспортируя шаблон управления ресурсами Azure.

## <a name="open-a-job-in-vs-code"></a>Открыть работу в VS Code

Прежде чем экспортировать шаблон, необходимо сначала открыть существующую работу Stream Analytics в Visual Studio Code. 

Чтобы экспортировать задание в локальный проект, найдите вакансию, которую вы хотите экспортировать в **Stream Analytics Explorer** на портале Azure. На странице **Запроса** выберите **Open in Visual Studio**. Затем выберите **визуальный код студии**.

![Работа Open Stream Analytics в коде Visual Studio](./media/resource-manager-export/open-job-vs-code.png)

Для получения дополнительной информации об использовании Visual [Visual Studio Code quickstart](quick-create-vs-code.md)Studio Code для управления рабочими местами Stream Analytics см.

## <a name="compile-the-script"></a>Компиляция скрипта 

Следующим шагом является компиляция шаблона задания в шаблон Azure Resource Manager. Прежде чем компилировать сценарий, убедитесь, что ваша работа имеет по крайней мере один вход и один выход настроен. Если вход ные или выходные не настроены, необходимо сначала настроить вход ные и выходные данные.

1. В Visual Studio Code перейдите к файлу *Transformation.asaql* вашей работы.

   ![Файл Transformation.asaql в коде Visual Studio](./media/resource-manager-export/transformation-asaql.png)

1. Нажмите правой кнопкой мыши на файл *Transformation.asaql* и выберите **ASA: Составить сценарий** из меню.

1. Обратите внимание, что папка **Deploy** отображается в рабочей области работы Stream Analytics.

1. Исследуйте файл *JobTemplate.json,* который является шаблоном управления ресурсами Azure, используемым для развертывания.

## <a name="complete-the-parameters-file"></a>Завершение файла параметров

Затем завершите файл параметров шаблона управления ресурсами Azure.

1. Откройте файл *JobTemplate.parameters.json,* расположенный в папке **Deploy** рабочей области Стрим Аналитики в Visual Studio Code.

1. Обратите внимание, что вхотворные и выходные клавиши являются недействительными. Замените значения null с фактическими ключами доступа для вхотворных и выходных ресурсов.

1. Сохраните файл параметров.

## <a name="deploy-using-templates"></a>Развертывание с помощью шаблонов

Вы готовы развернуть задание Azure Stream Analytics с помощью шаблонов Управления ресурсами Azure, созданных в предыдущем разделе.

В окне PowerShell запустите следующую команду. Не забудьте пересмотреть *ResourceGroupName*, *TemplateFile*, и *TemplateParameterFile* с вашим фактическим названием группы ресурсов, и полный путь файла к *JobTemplate.json* и *JobTemplate.parameters.json* файлы в **Развертывание Folder** вашего рабочего пространства.

Если у вас нет настроенной Azure PowerShell, выполните последующие действия в [модуле Установить Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Дальнейшие действия

* [Тестирование Лазурный поток Analytics рабочих мест локально с живым вхотвочонным использованием Visual Studio code](visual-studio-code-local-run-live-input.md)

* [Исследуйте вакансии Azure Stream Analytics с помощью визуального кода студии (Предварительный просмотр)](visual-studio-code-explore-jobs.md)