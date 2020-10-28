---
title: Как создать и подписать файл политики аттестации Azure
description: Объясняется, как создать и подписать политику аттестации.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: c8ffdcd0615913649e80b20f6873d005f4ad4410
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675987"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>Как создать и подписать файл политики аттестации

Политика аттестации — это файл, отправленный для аттестации Microsoft Azure. Аттестация Azure обеспечивает гибкость при отправке политики в формате, специально предназначенном для аттестации. Также возможна передача закодированной версии политики, JSON Web Signature. За написание политики аттестации отвечает администратор политики. В большинстве сценариев аттестации в качестве администратора политики выступает проверяющая сторона. Клиент, выполняющий вызов аттестации, отправляет свидетельство об аттестации, которое служба анализирует и преобразует во входящие утверждения (набор свойств, значение). Затем служба обрабатывает утверждения в зависимости от того, что определено в политике, и возвращает вычисленный результат.

Политика содержит правила, определяющие критерии авторизации, свойства и содержимое маркера аттестации. Пример файла политики выглядит следующим образом:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Файл политики содержит три сегмента, как показано выше:

- **version**  —  номер версии используемой грамматики. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Сейчас поддерживается только одна версия: 1.0.

- **authorizationrules**  — коллекция правил утверждений, которые будут проверяться первыми, чтобы определить, следует ли продолжать аттестацию Azure в сегменте **issuancerules** . Правила утверждений применяются в том порядке, в котором они определены.

- **issuancerules**  — коллекция правил утверждений, которые будут оцениваться для добавления дополнительных сведений в результат аттестации, как определено в политике. Правила утверждений являются необязательными и применяются в том порядке, в котором они определены.

Дополнительные сведения см. в разделе [Утверждения и правила утверждений](claim-rule-grammar.md).
   
## <a name="drafting-the-policy-file"></a>Создание файла политики

1. Создайте новый файл.
1. Добавьте версию в файл.
1. Добавьте разделы для **authorizationrules** и **issuancerules** .

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Правила авторизации содержат действие deny() без каких-либо условий, чтобы правила выпуска не обрабатывались. Кроме того, правило авторизации может также содержать действие allow(), разрешающее обработку правил выпуска.
  
4. Добавьте правила утверждений в правила авторизации.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Если входящий набор утверждений содержит утверждение, соответствующее типу, значению и издателю, действие permit() сообщит обработчику политики, что нужно обработать раздел **issuancerules** .
  
5. Добавьте правила утверждений в раздел **issuancerules** .

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Набор исходящих утверждений будет содержать утверждение с такой строкой:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Аналогичным образом можно создавать сложные политики. Дополнительные сведения см. в [примерах политики аттестации](policy-examples.md).
  
6. Сохраните файл.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Создание файла политики в формате JSON Web Signature

После создания файла политики, чтобы отправить политику в формате JWS, выполните следующие действия.

1. Создайте файл JWS (RFC 7515) с политикой (в кодировке UTF-8) в качестве полезных данных.
     - Идентификатор полезных данных для политики, закодированной в Base64Url, должен быть указан как AttestationPolicy.
     
     Пример JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. Подпишите политику (необязательно). Аттестация Azure поддерживает следующие алгоритмы.
     - **Нет**  — не подписывать полезные данные политики.
     - **RS256**  — поддерживаемый алгоритм для подписи полезных данных политики.

3. Отправьте JWS и проверьте политику.
     - Если в файле политики нет синтаксических ошибок, файл политики будет принят службой.
     - Если файл политики содержит синтаксические ошибки, то служба отклонит файл политики.

## <a name="signing-the-policy"></a>Подписывание политики

Ниже приведен пример скрипта Python для выполнения операции подписывания политики.

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>Дальнейшие действия
- [Настройка службы "Аттестация Azure" с помощью PowerShell](quickstart-powershell.md)
- [Аттестация анклава SGX с помощью примеров кода](/samples/browse/?expanded=azure&terms=attestation)