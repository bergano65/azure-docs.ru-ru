---
title: Преобразование содержимого сертификата в Base-64 — Azure HDInsight
description: Преобразование содержимого сертификата субъекта-службы в формат строки в формате Base64 64 в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894172"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Преобразование содержимого сертификата субъекта-службы в формат строки с кодировкой Base 64 в HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Появится сообщение об ошибке, сообщающее, что входные данные не являются допустимой строкой Base-64, так как она содержит небазовый символ 64, больше двух символов заполнения или символ, не являющийся пробелом, между символами заполнения.

## <a name="cause"></a>Причина:

При использовании PowerShell или развертывания шаблона Azure для создания кластеров с Data Lake в качестве основного или дополнительного хранилища, содержимое сертификата субъекта-службы, предоставленное для доступа к учетной записи хранения Data Lake, имеет формат Base-64. Неправильное преобразование содержимого PFX-сертификата в строку в кодировке Base 64 может привести к этой ошибке.

## <a name="resolution"></a>Разрешение

Получив сертификат субъекта-службы в формате PFX [(см. пример шагов](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) по созданию субъекта-службы), используйте следующую команду PowerShell или фрагмент кода C# для преобразования содержимого сертификата в формат base-64.

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

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
