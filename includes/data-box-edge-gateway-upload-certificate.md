---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448633"
---
Действительный сертификат SSL гарантирует, что вы отправляете зашифрованные сведения к требуемому серверу. Помимо шифрования этот сертификат позволяет также для проверки подлинности. Вы можете отправить свои собственные доверенный SSL-сертификат в интерфейсе PowerShell устройства.

1. [Подключение к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Set-HcsCertificate` командлет, чтобы отправить сертификат. При запросе укажите следующие параметры:

   - `CertificateFilePath` — Путь к общей папке, содержащей файл сертификата в *.pfx* формат.
   - `CertificatePassword` Пароль, используемый для защиты сертификата.
   - `Credentials` — Имя пользователя для доступа к папке с сертификатом. Введите пароль в общую сетевую папку при появлении запроса.

     В следующем примере показано использование этого командлета:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

