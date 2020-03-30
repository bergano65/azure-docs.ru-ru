---
title: Преобразование содержимого сертификата на базовый-64 - Azure HDInsight
description: Преобразование содержимого основного сертификата службы в закодированный формат строки base-64 в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894172"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Преобразование содержимого основного сертификата службы в закодированный формат строки базового-64 в HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Вы получаете сообщение об ошибке о том, что вход ный вход не является допустимой строкой Base-64, поскольку он содержит небазовый символ 64, более двух символов обивки или небелый космический символ среди символов обивки.

## <a name="cause"></a>Причина

При использовании развертывания шаблонов PowerShell или Azure для создания кластеров с Data Lake в качестве основного или дополнительного хранилища основное содержимое сертификата службы, предоставляемого для доступа к учетной записи хранилища Data Lake, находится в формате базового-64. Неправильное преобразование содержимого сертификата pfx в закодированную строку base-64 может привести к этой ошибке.

## <a name="resolution"></a>Решение

Если у вас есть основной сертификат службы в формате pfx (см. [здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) для примера основных этапов создания службы), используйте следующую команду PowerShell или фрагмент C' для преобразования содержимого сертификата в формат базового-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
