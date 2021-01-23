---
title: Хранение и использование сертификатов в Облачных службах Azure (расширенная поддержка)
description: Процессы хранения и использования сертификатов в облачных службах Azure (Расширенная поддержка)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9e69b4e9279f9147c2ee13d42a42aec0c5a15d96
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744727"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Использование сертификатов с облачными службами Azure (Расширенная поддержка)

Key Vault используется для хранения сертификатов, связанных с облачными службами (Расширенная поддержка). Хранилища ключей можно создавать с помощью [портал Azure](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) и [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell). Добавьте сертификаты в Key Vault, а затем сослаться на отпечатки сертификата в файле конфигурации службы. Также необходимо включить Key Vault для соответствующих разрешений, чтобы ресурс облачных служб (Расширенная поддержка) мог получить сертификат, хранящийся как секреты из Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Отправка сертификата в Key Vault 

1.  Войдите в портал Azure и перейдите к Key Vault. Если вы не настроили Key Vault, вы можете создать его в этом же окне.

2. Выбор **политик доступа**

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="На рисунке показано, как выбрать политики доступа в колонке хранилища ключей.":::

3. Убедитесь, что политики доступа включают следующие свойства:
    - **Разрешение доступа к виртуальным машинам Azure для развертывания**
    - **Разрешение доступа к Azure Resource Manager для развертывания шаблона** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Изображение отображает окно политики доступа в портал Azure.":::
 
4.  Выбор **сертификатов** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="На рисунке показано, как выбрать параметр сертификаты в окне &quot;политики хранилища ключей&quot; в портал Azure.":::

5. Выбор **создания/импорта**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="На рисунке показано, как выбрать параметр &quot;создать/импортировать&quot;":::

4.  Заполните необходимые сведения, чтобы завершить отправку сертификата. 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="На рисунке показано окно импорта в портал Azure.":::

5.  Добавьте сведения о сертификате в роль в файле конфигурации службы (CSCFG-файл). Убедитесь, что отпечаток сертификата в портал Azure соответствует отпечатку в файле конфигурации службы (cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>Следующие шаги 
- Ознакомьтесь с [предварительными требованиями для развертывания](deploy-prerequisite.md) облачных служб (Расширенная поддержка).
- Ознакомьтесь с [часто задаваемыми вопросами](faq.md) о облачных службах (Расширенная поддержка).
- Разверните облачную службу (расширенную поддержку) с помощью [портал Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), [шаблона](deploy-template.md) или [Visual Studio](deploy-visual-studio.md).