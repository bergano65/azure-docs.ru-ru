---
title: 'Мониторинг диагностики Azure: Аттестация Azure'
description: Мониторинг диагностики Azure для Аттестации Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606009"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Настройка диагностики с использованием конечной точки доверенного платформенного модуля Аттестации Azure

[Журналы платформы](/azure/azure-monitor/platform/platform-logs-overview) в Azure, в том числе журнал действий Azure и журналы ресурсов, предоставляют подробные сведения о диагностике и аудите для ресурсов Azure и платформы Azure, от которых они зависят. [Метрики платформы](/azure/azure-monitor/platform/data-platform-metrics) собираются по умолчанию и обычно хранятся в базе данных метрик Azure Monitor. В этой статье приведены сведения о создании и настройке параметров диагностики для отправки метрик и журналов платформы в разные назначения. 

Служба конечной точки доверенного платформенного модуля поддерживает параметр диагностики и позволяет выполнять мониторинг действий. Чтобы настроить [мониторинг Azure](/azure/azure-monitor/overview) для конечной точки службы доверенного платформенного модуля с помощью PowerShell, выполните указанные ниже действия. 

Настройте службу "Аттестация Azure". 

[Настройка службы "Аттестация Azure" с помощью Azure PowerShell](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Журналы действий можно найти в разделе "Контейнеры" учетной записи хранения. Подробные сведения можно найти в статье о [сборе журналов ресурсов из ресурса Azure и их анализе в Azure Monitor](/azure/azure-monitor/learn/tutorial-resource-logs).
