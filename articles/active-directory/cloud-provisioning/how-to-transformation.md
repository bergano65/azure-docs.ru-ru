---
title: Azure AD Connect преобразования подготовки облака
description: В этом документе описывается использование преобразований для изменения сопоставлений атрибутов по умолчанию.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794475"
---
# <a name="transformations"></a>Преобразования

Преобразование позволяет изменить поведение по умолчанию при синхронизации атрибута с Azure AD с помощью подготовки облака.  

Чтобы выполнить эту задачу, необходимо изменить схему, а затем повторно отправить ее через веб-запрос.

Дополнительные сведения об атрибутах подготовки облака см. в статье [Основные сведения о схеме Azure AD](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Получение схемы
Чтобы получить схему, выполните действия, описанные в разделе [Просмотр схемы](concept-attributes.md#viewing-the-schema). 


## <a name="custom-attribute-mapping"></a>Сопоставление настраиваемых атрибутов
Чтобы добавить настраиваемое сопоставление атрибутов, используйте следующую процедуру.

1. Скопируйте схему в текст или редактор кода, например [Visual Studio Code](https://code.visualstudio.com/).  
2. Укажите объект, который необходимо обновить в схеме ![](media/how-to-transformation/transform1.png)</br>
3. Выберите код для **ExtensionAttribute3** в объекте User.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 4.  Измените код, чтобы атрибут Company был сопоставлен с ExtensionAttribute3.
    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([company])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[company]",
                                                "name": "company",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 5. Скопируйте схему обратно в обозреватель Graph, измените тип запроса на "разместить и **выполнить запрос**".  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  Теперь в портал Azure перейдите к конфигурации подготовки облака и **перезапустите подготовку**.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  Через некоторое время убедитесь, что атрибуты заполнены, выполнив следующий запрос в проводнике Graph: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 8.  Теперь вы увидите значение.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>Сопоставление настраиваемых атрибутов с функцией
Для более расширенного сопоставления можно использовать функции, позволяющие управлять данными и создавать значения для атрибутов в соответствии с потребностями Организации.

Для выполнения этой задачи просто выполните описанные выше действия, а затем измените функцию, которая используется для создания окончательного значения.

Дополнительные сведения о синтаксисе и примерах выражений см. [в разделе Написание выражений для сопоставлений атрибутов в Azure Active Directory](reference-expressions.md)


## <a name="next-steps"></a>Дальнейшие действия 

- [Что такое подготовка?](what-is-provisioning.md)
- [Что такое Azure AD Connect подготовки облака?](what-is-cloud-provisioning.md)
