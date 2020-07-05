---
title: null 許容値型
ms.date: 07/20/2015
f1_keywords:
- vb.Nullable
helpviewer_keywords:
- nullable types [Visual Basic]
- '? [Visual Basic]'
- types [Visual Basic], nullable
- nullable types [Visual Basic]
- data types [Visual Basic], nullable
ms.assetid: 9ac3b602-6f96-4e6d-96f7-cd4e81c468a6
ms.openlocfilehash: beed8262c50dc68330b8f03aa3d864ed2f8df0d5
ms.sourcegitcommit: 99b153b93bf94d0fecf7c7bcecb58ac424dfa47c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2020
ms.locfileid: "80249683"
---
# <a name="nullable-value-types-visual-basic"></a>null 許容値型 (Visual Basic)

場合によっては、特定の状況で値が定義されていない値型を使用することがあります。 たとえば、データベースのフィールドでは、意味のある値が割り当てられている場合と、値が割り当てられていない場合を、区別することが必要なときがあります。 値型は、通常の値または null 値を受け取るように拡張できます。 このような拡張機能は "*null 許容型*" と呼ばれます。

各 null 許容値型は、ジェネリック構造体 <xref:System.Nullable%601> から作成されます。 作業関連のアクティビティを追跡するデータベースについて考えてみます。 次の例では、null 許容 `Boolean` 型が作成され、その型の変数が宣言されます。 宣言は、次の 3 つの方法で記述できます。

[!code-vb[VbVbalrNullableValue#1](../../../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrNullableValue/VB/Class1.vb#1)]

変数 `ridesBusToWork` では、`True` の値または `False` の値を保持するか、または値をまったく保持しないことができます。 既定の初期値は値なしです。この場合は、このユーザーに対して情報がまだ取得されていないことを意味します。 これに対し、`False` は情報が取得されていて、ユーザーが通勤にバスを利用しないことを意味します。

null 許容値型を使用して変数とプロパティを宣言したり、null 許容値型の要素を含む配列を宣言したりできます。 パラメーターが null 許容値型であるプロシージャを宣言したり、`Function` プロシージャから null 許容値型を返したりすることができます。

配列、`String`、クラスなどの参照型では、null 許容型を作成できません。 基になる型は、値型である必要があります。 詳細については、「 [Value Types and Reference Types](value-types-and-reference-types.md)」を参照してください。

## <a name="using-a-nullable-type-variable"></a>null 許容型変数の使用

null 許容値型の最も重要なメンバーは、<xref:System.Nullable%601.HasValue%2A> プロパティと <xref:System.Nullable%601.Value%2A> プロパティです。 null 許容値型の変数の場合、<xref:System.Nullable%601.HasValue%2A> により、変数に定義済みの値が含まれているかどうかがわかります。 <xref:System.Nullable%601.HasValue%2A> が `True` の場合は、<xref:System.Nullable%601.Value%2A> から値を読み取ることができます。 <xref:System.Nullable%601.HasValue%2A> と <xref:System.Nullable%601.Value%2A> はどちらも `ReadOnly` プロパティであることに注意してください。

### <a name="default-values"></a>既定値

null 許容値型の変数を宣言すると、その <xref:System.Nullable%601.HasValue%2A> プロパティには既定値の `False` が設定されます。 これは、既定では、変数には、基になる値型の既定値が設定されるのではなく、定義された値がないことを意味します。 次の例では、`Integer` 型の既定値が 0 であっても、変数 `numberOfChildren` の初期状態には定義された値がありません。

[!code-vb[VbVbalrNullableValue#2](../../../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrNullableValue/VB/Class1.vb#2)]

null 値は、未定義の値または不明な値を示すのに役立ちます。 `numberOfChildren` が `Integer` として宣言されている場合、情報が現在使用できないことを示す値は存在しません。

### <a name="storing-values"></a>値の格納

null 許容値型の変数またはプロパティへの値の格納は、通常の方法で行います。 次の例では、前の例で宣言した `numberOfChildren` 変数に値を代入しています。

[!code-vb[VbVbalrNullableValue#3](../../../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrNullableValue/VB/Class1.vb#3)]

null 許容値型の変数またはプロパティに定義された値が含まれている場合は、値が割り当てられていない初期状態に戻すことができます。 これを行うには、次の例に示すように、変数またはプロパティに `Nothing` を設定します。

[!code-vb[VbVbalrNullableValue#4](../../../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrNullableValue/VB/Class1.vb#4)]

> [!NOTE]
> null 許容値型の変数に `Nothing` を割り当てることはできますが、等号を使用して `Nothing` かどうかをテストすることはできません。 等号を使用する比較 (`someVar = Nothing`) は、常に `Nothing` と評価されます。 変数の <xref:System.Nullable%601.HasValue%2A> プロパティが `False` かどうかをテストすることや、`Is` または `IsNot` 演算子を使用してテストすることはできます。

### <a name="retrieving-values"></a>値の取得

null 許容値型の変数の値を取得するには、最初にその <xref:System.Nullable%601.HasValue%2A> プロパティをテストして、値があることを確認する必要があります。 <xref:System.Nullable%601.HasValue%2A> が `False` のときに値を読み込もうとすると、Visual Basic で <xref:System.InvalidOperationException> 例外がスローされます。 次の例では、前の例の変数 `numberOfChildren` を読み取るための推奨される方法を示します。

[!code-vb[VbVbalrNullableValue#5](../../../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrNullableValue/VB/Class1.vb#5)]

## <a name="comparing-nullable-types"></a>null 許容型の比較

Null 許容の `Boolean` 変数をブール式で使用すると、結果は `True`、`False`、または `Nothing` になります。 `And` と `Or` の真理値表を次に示します。 `b1` と `b2` が持つことのできる値は 3 つなので、評価する組み合わせは 9 つです。

|b1|b2|b1 And b2|b1 Or b2|
|--------|--------|---------------|--------------|
|`Nothing`|`Nothing`|`Nothing`|`Nothing`|
|`Nothing`|`True`|`Nothing`|`True`|
|`Nothing`|`False`|`False`|`Nothing`|
|`True`|`Nothing`|`Nothing`|`True`|
|`True`|`True`|`True`|`True`|
|`True`|`False`|`False`|`True`|
|`False`|`Nothing`|`False`|`Nothing`|
|`False`|`True`|`False`|`True`|
|`False`|`False`|`False`|`False`|

ブール型の変数または式の値が `Nothing` の場合は、`True` でも `False` でもありません。 例を次に示します。

[!code-vb[VbVbalrNullableValue#6](../../../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrNullableValue/VB/Class1.vb#6)]

この例では、`b1 And b2` は `Nothing` と評価されます。 その結果、各 `If` ステートメントでは `Else` 句が実行され、出力は次のようになります。

`Expression is not True`

`Expression is not False`

> [!NOTE]
> ショートサーキット評価を使用する `AndAlso` および `OrElse` では、最初が `Nothing` と評価されたときに、2 番目のオペランドを評価する必要があります。

## <a name="propagation"></a>伝達

算術、比較、シフト、または型の演算の一方または両方のオペランドが null 許容値型である場合は、演算の結果も null 許容値型になります。 両方のオペランドの値が `Nothing` ではない場合、演算は、どちらも null 許容値型でない場合と同じように、オペランドの基になる値に対して実行されます。 次の例では、変数 `compare1` と `sum1` は暗黙的に型指定されています。 これらをマウスでポイントすると、どちらもコンパイラによって null 許容値型と推論されていることがわかります。

[!code-vb[VbVbalrNullableValue#7](../../../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrNullableValue/VB/Class1.vb#7)]

一方または両方のオペランドの値が `Nothing` の場合、結果は `Nothing` になります。

[!code-vb[VbVbalrNullableValue#8](../../../../../samples/snippets/visualbasic/VS_Snippets_VBCSharp/VbVbalrNullableValue/VB/Class1.vb#8)]

## <a name="using-nullable-types-with-data"></a>データでの null 許容型の使用

データベースは、null 許容値型を使用する最も重要な場所の 1 つです。 現在は、すべてのデータベース オブジェクトで null 許容値型がサポートされているわけではありませんが、デザイナーで生成されたテーブル アダプターではサポートされています。 「[TableAdapter での Null 許容型のサポート](/visualstudio/data-tools/fill-datasets-by-using-tableadapters#tableadapter-support-for-nullable-types)」を参照してください。

## <a name="see-also"></a>関連項目

- <xref:System.InvalidOperationException>
- <xref:System.Nullable%601.HasValue%2A>
- [データの種類](index.md)
- [Value Types and Reference Types](value-types-and-reference-types.md)
- [トラブルシューティング (データ型)](troubleshooting-data-types.md)
- [TableAdapters を使用してデータセットを入力する](/visualstudio/data-tools/fill-datasets-by-using-tableadapters)
- [If 演算子](../../../language-reference/operators/if-operator.md)
- [ローカル型の推論](../variables/local-type-inference.md)
- [Is 演算子](../../../language-reference/operators/is-operator.md)
- [IsNot 演算子](../../../language-reference/operators/isnot-operator.md)
- [null 許容値型 (C#)](../../../../csharp/language-reference/builtin-types/nullable-value-types.md)
