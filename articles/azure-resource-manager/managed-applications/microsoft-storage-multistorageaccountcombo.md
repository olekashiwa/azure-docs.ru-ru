---
title: Элемент пользовательского интерфейса MultiStorageAccountCombo
description: Сведения об элементе пользовательского интерфейса Microsoft.Storage.MultiStorageAccountCombo для портала Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651882"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Storage.MultiStorageAccountCombo

Группа элементов управления для создания нескольких учетных записей хранения с именами, которые начинаются с общего префикса.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

![Элемент пользовательского интерфейса Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>схема

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Remarks

- Значение для `defaultValue.prefix` объединяется с одним или несколькими целыми числами для создания последовательности имен учетных записей хранения. Например, если для параметра `defaultValue.prefix` задано значение **sa**, а для параметра `count` — **2**, тогда для учетных записей хранения создаются имена **sa1** и **sa2**. Созданные имена учетных записей хранения автоматически проверяются на уникальность.
- Имена учетных записей хранения формируются лексикографически на основе `count`. Например, если для параметра `count` задано значение 10, тогда имена учетных записей хранения будут заканчиваться на двухзначное число (01, 02, 03 и т. д.).
- Значением по умолчанию для параметра `defaultValue.prefix` является **null**, а для параметра `defaultValue.type` — **Premium_LRS**.
- Любой тип, не указанный в `constraints.allowedTypes`, скрыт, а любой тип, не указанный в `constraints.excludedTypes`, отображается. Параметры `constraints.allowedTypes` и `constraints.excludedTypes` являются необязательными. При этом их нельзя использовать одновременно.
- Кроме создания имен учетных записей хранения, параметр `count` используется, чтобы задать соответствующее число для элемента. Он поддерживает статическое значение, например **2**, или динамическое значение из другого элемента, например `[steps('step1').storageAccountCount]`. Значение по умолчанию — **1**.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
