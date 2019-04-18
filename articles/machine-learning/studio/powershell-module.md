---
title: Модули PowerShell для Студии машинного обучения
titleSuffix: Azure Machine Learning Studio
description: Использование PowerShell для создания рабочих областей, экспериментов, веб-служб и другого, а также управления этими компонентами в Студии машинного обучения Microsoft Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: 4d867a8befb9333ebf33b9ac7ba179e25f0b9f9b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698577"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Модули PowerShell для Студии машинного обучения Microsoft Azure

С помощью модулей PowerShell вы можете программным способом управлять ресурсами Студии, например, рабочими областями, наборами данных и веб-службами.

Вы можете взаимодействовать с ресурсами Студии с помощью трех модулей Powershell:

* [Azure PowerShell Az](#az-rm) 2018 года выпуска содержит все функции AzureRM, а также различные имена командлетов.
* [AzureRM](#az-rm) в выпуске 2016, заменены PowerShell Az
* [Классический PowerShell для Машинного обучения Azure](#classic) 2016 года выпуска.

Несмотря на то, что эти модули PowerShell имеют некоторые сходства, каждый предназначен для определенных сценариев. В этой статье описаны различия между модулями PowerShell, а также предложена помощь в выборе подходящих вам модулей.  

Проверьте [таблицу поддержки](#support-table) указанную ниже, чтобы увидеть, какие ресурсы поддерживаются каждым из модулей. 

## <a name="az-rm"></a> Azure PowerShell Az и AzureRM

Az теперь является предполагаемым модулем PowerShell для взаимодействия с Azure и включает все прежние функции AzureRM. AzureRM продолжит получать исправления ошибки, но не будет получать новые командлеты или функции.  AZ и AzureRM управляют решениями развернутых служб с помощью модуля **Azure Resource Manager**. Эти ресурсы включают рабочие области студии и «New» веб-службы студии. 

PowerShell — Классическая модель может устанавливаться параллельно с Az или AzureRM, чтобы охватить оба этих типа ресурсов «new» и «классический». Тем не менее не рекомендуется устанавливать Az и AzureRM одновременно. Чтобы выбрать между Az и AzureRM, Microsoft рекомендует Az для всех последующих развернутых служб.  Дополнительные сведения о Az и AzureRM и перехода в [введение в Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Чтобы начать работу с использованием команды Az, выполните действия, описанные в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> Классический PowerShell

[Классическая модель PowerShell](https://aka.ms/amlps) Студии позволяет управлять развернутыми ресурсами, используя **классическую модель развертывания**. Эти ресурсы включают активы пользователей Studio, «классических» веб-служб и конечных точек службы «классических».

Тем не менее Корпорация Майкрософт рекомендует использовать модель развертывания Resource Manager для всех будущих ресурсов для упрощения развертывания и управления ресурсами. Дополнительные сведения о моделях развертывания см. в статье [Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Чтобы начать работу с модулем PowerShell для машинного обучения, скачайте [пакет выпуска](https://github.com/hning86/azuremlps/releases) из GitHub и следуйте приведенным [здесь](https://github.com/hning86/azuremlps/blob/master/README.md) инструкциям по установке. В инструкциях показано, как разблокировать скачанную и распакованную библиотеку DLL, а затем импортировать ее в среду PowerShell.

PowerShell — Классическая модель может устанавливаться параллельно с Az или AzureRM, чтобы охватить оба этих типа ресурсов «new» и «классический».

## <a name="support-table"></a> Таблица поддержки PowerShell

 **Рабочие области Студии** | **Az** |  **AzureRM** | **PowerShell — классическая модель** |
| --- | --- | --- | --- |
| Создавать и удалять рабочие области | [Шаблоны Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Шаблоны Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Управление пользователями рабочей области |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Управление тарифными планами с обязательствами | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | New-AzureRmMlCommitmentPlan |
|||
| **Веб-службы** | **Az** | **AzureRM** | **PowerShell — классическая модель** |
| Управление веб-службами | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> («new» веб-службы) | New-AzureRmMlWebService <br> («new» веб-службы) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> («классический» веб-службы) |
| Управление конечными точками и ключами |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> («new» веб-службы) | Get-AzureRmMlWebServiceKeys <br> («new» веб-службы) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> («классический» веб-службы) |
|||
| **Ресурсы пользователя** | **Az** | **AzureRM** | **PowerShell — классическая модель** |
| Управление наборами данных и обученными моделями |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Управление экспериментами |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Управление настраиваемыми модулями |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Дальнейшие действия
См. в полной документации этих модуль PowerShell:
* [PowerShell — классическая модель](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
