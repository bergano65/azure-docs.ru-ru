---
title: Преобразование содержимого сертификата в Base-64 — Azure HDInsight
description: Преобразование содержимого сертификата субъекта-службы в формат строки в формате Base64 64 в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 88faf878d9528b314e59c81dad7a97d4700b608f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289129"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Преобразование содержимого сертификата субъекта-службы в формат строки с кодировкой Base 64 в HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Появится сообщение об ошибке, сообщающее, что входные данные не являются допустимой строкой Base-64, так как она содержит небазовый символ 64, больше двух символов заполнения или символ, не являющийся пробелом, между символами заполнения.

## <a name="cause"></a>Причина

При использовании PowerShell или развертывания шаблона Azure для создания кластеров с Data Lake в качестве основного или дополнительного хранилища, содержимое сертификата субъекта-службы, предоставленное для доступа к учетной записи хранения Data Lake, имеет формат Base-64. Неправильное преобразование содержимого PFX-сертификата в строку в кодировке Base 64 может привести к этой ошибке.

## <a name="resolution"></a>Решение

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]