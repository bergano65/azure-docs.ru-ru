---
title: Модули PowerShell
titleSuffix: ML Studio (classic) - Azure
description: Используйте PowerShell для создания и управления рабочими областями, экспериментами, веб-службами и т. д. Машинное обучение Azure Studio (классическая модель).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: c1f9a9248494344cd6577ca2bf743a4c80730cda
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837996"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Модули PowerShell для Машинное обучение Azure Studio (классическая модель)

С помощью модулей PowerShell можно программно управлять ресурсами и активами студии (классическими), такими как рабочие области, наборы данных и веб-службы.

Вы можете взаимодействовать с классической версией ресурсов Studio с помощью трех модулей PowerShell:

* [Azure PowerShell Az](#az-rm) 2018 года выпуска содержит все функции AzureRM, а также различные имена командлетов.
* [AzureRM](#az-rm) , выпущенная в 2016, заменена PowerShell AZ
* [Классический PowerShell для Машинного обучения Azure](#classic) 2016 года выпуска.

Хотя эти модули PowerShell имеют некоторые сходства, каждый из них предназначен для определенных сценариев. В этой статье описаны различия между модулями PowerShell, а также предложена помощь в выборе подходящих вам модулей.  

Проверьте [таблицу поддержки](#support-table) указанную ниже, чтобы увидеть, какие ресурсы поддерживаются каждым из модулей. 

## <a name="az-rm"></a> Azure PowerShell Az и AzureRM

Az теперь является предполагаемым модулем PowerShell для взаимодействия с Azure и включает все прежние функции AzureRM. AzureRM продолжит получать исправления ошибки, но не будет получать новые командлеты или функции.  AZ и AzureRM управляют решениями развернутых служб с помощью модуля **Azure Resource Manager**. К этим ресурсам относятся рабочие области Studio (классические) и Studio (классическая модель) "новые веб-службы". 

Классическую программу PowerShell можно установить вместе с az или AzureRM, чтобы охватить как "новые", так и "классические" типы ресурсов. Тем не менее не рекомендуется устанавливать Az и AzureRM одновременно. Чтобы выбрать между Az и AzureRM, Microsoft рекомендует Az для всех последующих развернутых служб.  Дополнительные сведения о AZ и AzureRM и пути перехода см. в статье [Введение в Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Чтобы начать работу с использованием команды Az, выполните действия, описанные в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> Классический PowerShell

[Классический модуль PowerShell](https://aka.ms/amlps) для Studio (классическая модель) позволяет управлять ресурсами, развернутыми с помощью **классической модели развертывания**. В число этих ресурсов входят пользовательские активы (классические) Studio, "классические" веб-службы и "классические" конечные точки веб-служб.

Однако корпорация Майкрософт рекомендует использовать модель развертывания диспетчер ресурсов для всех будущих ресурсов, чтобы упростить развертывание и управление ресурсами. Дополнительные сведения о моделях развертывания см. в статье [Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Чтобы начать работу с модулем PowerShell для машинного обучения, скачайте [пакет выпуска](https://github.com/hning86/azuremlps/releases) из GitHub и следуйте приведенным [здесь](https://github.com/hning86/azuremlps/blob/master/README.md) инструкциям по установке. В инструкциях показано, как разблокировать скачанную и распакованную библиотеку DLL, а затем импортировать ее в среду PowerShell.

Классическую программу PowerShell можно установить вместе с az или AzureRM, чтобы охватить как "новые", так и "классические" типы ресурсов.

## <a name="support-table"></a> Таблица поддержки PowerShell


| | **Az** |  **PowerShell — классическая модель** |
| --- | --- | --- |
| Создание и удаление рабочих областей | [Шаблоны Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Управление планами обязательств рабочей области | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Управление пользователями рабочей области |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Управление веб-службами | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("новые" веб-службы)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("классические" веб-службы) |
| Управление конечными точками или ключами веб-службы |  [Get-Азмлвебсервицекэй](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Управление пользовательскими наборами данных и обученными моделями| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Управление экспериментами пользователей |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Управление настраиваемыми модулями | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с полной документацией по этому модулю PowerShell:
* [PowerShell — классическая модель](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
