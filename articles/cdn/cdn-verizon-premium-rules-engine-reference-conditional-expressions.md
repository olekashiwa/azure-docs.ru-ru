---
title: Условные выражения для обработчика правил Azure CDN-Verizon Premium
description: Справочная документация по Azure CDN из обработчика правил Verizon Premium, соответствующих условиям и функциям.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082974"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN из условных выражений обработчика правил Verizon Premium

В этой статье приведены подробные описания условных выражений для [обработчика правил](cdn-verizon-premium-rules-engine.md)сети доставки содержимого (CDN) Azure.

Первая часть правила — это условное выражение.

Условное выражение | ОПИСАНИЕ
-----------------------|-------------
IF | Выражение IF всегда входит в первую инструкцию в правиле. Как и другие условные выражения, инструкция IF должна быть связана с соответствием. Если дополнительные условные выражения не определены, это сопоставление определяет критерий, который должен быть удовлетворен, прежде чем к запросу можно будет применить набор функций.
AND IF | Выражение AND IF можно добавлять только после таких условных выражений, как IF и AND IF. Оно указывает на наличие другого условия, которое должно быть выполнено для первоначальной инструкции IF.
ELSE IF| Выражение ELSE IF указывает альтернативное условие, которое должно быть выполнено до выполнения набора определенных функций для этой инструкции ELSE IF. Инструкция ELSE IF указывает на конец предыдущей инструкции. После инструкции ELSE IF можно добавить только инструкцию ELSE IF. Это означает, что эту инструкцию можно использовать только для указания одного дополнительного условия, которое должно быть выполнено.

**Пример**: ![соответствие условию CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Последующее правило может переопределить действия, заданные предыдущим правилом.
   > Например, универсальное правило защищает все запросы путем аутентификации на основе маркеров. Под ним можно создать другое правило, чтобы сделать исключение для некоторых типов запросов.

## <a name="next-steps"></a>Дополнительная информация

- [Общие сведения о сети доставки содержимого Azure](cdn-overview.md)
- [Обработчик правил Azure CDN](cdn-verizon-premium-rules-engine-reference.md)
- [Условия соответствия обработчика правил](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Функции обработчика правил Azure CDN](cdn-verizon-premium-rules-engine-reference-features.md)
- [Переопределение стандартного поведения через HTTP с помощью обработчика правил](cdn-verizon-premium-rules-engine.md)