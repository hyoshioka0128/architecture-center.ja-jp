---
title: "具体化されたビュー"
description: "データの形式が必要なクエリ操作に適していない場合に、1 つ以上のデータ ストアのデータの事前設定されたビューを生成します。"
keywords: "設計パターン"
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- data-management
- performance-scalability
ms.openlocfilehash: 992abcb57204c65a7ca9e9e2525d3ea7339c4a2c
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
# <a name="materialized-view-pattern"></a><span data-ttu-id="8cf2f-104">具体化されたビュー パターン</span><span class="sxs-lookup"><span data-stu-id="8cf2f-104">Materialized View pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="8cf2f-105">データの形式が必要なクエリ操作に適していない場合に、1 つ以上のデータ ストアのデータの事前設定されたビューを生成します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-105">Generate prepopulated views over the data in one or more data stores when the data isn't ideally formatted for required query operations.</span></span> <span data-ttu-id="8cf2f-106">これにより、効率的なクエリとデータ抽出をサポートできるようになり、アプリケーションのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-106">This can help support efficient querying and data extraction, and improve application performance.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="8cf2f-107">コンテキストと問題</span><span class="sxs-lookup"><span data-stu-id="8cf2f-107">Context and problem</span></span>

<span data-ttu-id="8cf2f-108">データを保存するときに、開発者とデータ管理者は、多くの場合、データを読み取る方法ではなく、保存する方法を優先します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-108">When storing data, the priority for developers and data administrators is often focused on how the data is stored, as opposed to how it's read.</span></span> <span data-ttu-id="8cf2f-109">選択されるストレージ形式は、通常、データの形式、データ サイズとデータ整合性の管理の要件、使用されているストアの種類と密接に関連しています。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-109">The chosen storage format is usually closely related to the format of the data, requirements for managing data size and data integrity, and the kind of store in use.</span></span> <span data-ttu-id="8cf2f-110">たとえば、NoSQL ドキュメント ストアを使用する場合、データは一連の集計として表されることが多く、それぞれにそのエンティティのすべての情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-110">For example, when using NoSQL document store, the data is often represented as a series of aggregates, each containing all of the information for that entity.</span></span>

<span data-ttu-id="8cf2f-111">ただし、これはクエリに悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-111">However, this can have a negative effect on queries.</span></span> <span data-ttu-id="8cf2f-112">クエリで一部のエンティティのデータのサブセットだけが必要な場合 (注文の詳細がまったく含まれていない、複数の顧客の注文の概要など)、必要な情報を取得するために、関連するエンティティのすべてのデータを抽出する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-112">When a query only needs a subset of the data from some entities, such as a summary of orders for several customers without all of the order details, it must extract all of the data for the relevant entities in order to obtain the required information.</span></span>

## <a name="solution"></a><span data-ttu-id="8cf2f-113">解決策</span><span class="sxs-lookup"><span data-stu-id="8cf2f-113">Solution</span></span>

<span data-ttu-id="8cf2f-114">効率的なクエリをサポートするには、一般的な解決策として、必要な結果セットに適した形式でデータを具体化するビューを事前に生成します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-114">To support efficient querying, a common solution is to generate, in advance, a view that materializes the data in a format suited to the required results set.</span></span> <span data-ttu-id="8cf2f-115">具体化されたビュー パターンでは、ソース データがクエリに適した形式ではない環境、適切なクエリの生成が難しい環境、またはデータやデータ ストアの性質に起因してクエリのパフォーマンスが低い環境で、データの事前設定されたビューを生成します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-115">The Materialized View pattern describes generating prepopulated views of data in environments where the source data isn't in a suitable format for querying, where generating a suitable query is difficult, or where query performance is poor due to the nature of the data or the data store.</span></span>

<span data-ttu-id="8cf2f-116">これらの具体化されたビューには、クエリに必要なデータだけが含まれているため、アプリケーションは必要な情報をすばやく取得できます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-116">These materialized views, which only contain data required by a query, allow applications to quickly obtain the information they need.</span></span> <span data-ttu-id="8cf2f-117">テーブルの結合やデータ エンティティの組み合わせに加え、具体化されたビューには、計算列またはデータ項目の現在の値、データ項目の値の組み合わせまたは変換の実行の結果、クエリの一部として指定された値を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-117">In addition to joining tables or combining data entities, materialized views can include the current values of calculated columns or data items, the results of combining values or executing transformations on the data items, and values specified as part of the query.</span></span> <span data-ttu-id="8cf2f-118">具体化されたビューは、1 つのクエリだけを対象に最適化することもできます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-118">A materialized view can even be optimized for just a single query.</span></span>

<span data-ttu-id="8cf2f-119">重要なのは、具体化されたビューとそこに含まれるデータは、ソース データ ストアから完全に再構築できるため、完全に破棄可能であるという点です。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-119">A key point is that a materialized view and the data it contains is completely disposable because it can be entirely rebuilt from the source data stores.</span></span> <span data-ttu-id="8cf2f-120">具体化されたビューはアプリケーションによって直接更新されることはないので、特殊なキャッシュと言えます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-120">A materialized view is never updated directly by an application, and so it's a specialized cache.</span></span>

<span data-ttu-id="8cf2f-121">ビューのソース データが変更されたら、ビューを更新して新しい情報を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-121">When the source data for the view changes, the view must be updated to include the new information.</span></span> <span data-ttu-id="8cf2f-122">これは、自動的に実行されるようにスケジュールすることも、システムが元のデータの変更を検出したときに実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-122">You can schedule this to happen automatically, or when the system detects a change to the original data.</span></span> <span data-ttu-id="8cf2f-123">ビューを手動で再生成することが必要な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-123">In some cases it might be necessary to regenerate the view manually.</span></span> <span data-ttu-id="8cf2f-124">次の図は、具体化されたビュー パターンの使用方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-124">The figure shows an example of how the Materialized View pattern might be used.</span></span>

![図 1: 具体化されたビュー パターンの使用方法の例](./_images/materialized-view-pattern-diagram.png)


## <a name="issues-and-considerations"></a><span data-ttu-id="8cf2f-126">問題と注意事項</span><span class="sxs-lookup"><span data-stu-id="8cf2f-126">Issues and considerations</span></span>

<span data-ttu-id="8cf2f-127">このパターンの実装方法を決めるときには、以下の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-127">Consider the following points when deciding how to implement this pattern:</span></span>

<span data-ttu-id="8cf2f-128">ビューをいつ、どのようにして更新するか。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-128">How and when the view will be updated.</span></span> <span data-ttu-id="8cf2f-129">ソース データの変更を示すイベントに応じて再生成するのが理想的ですが、ソース データの変更のペースが速い場合は、過剰なオーバーヘッドが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-129">Ideally it'll regenerate in response to an event indicating a change to the source data, although this can lead to excessive overhead if the source data changes rapidly.</span></span> <span data-ttu-id="8cf2f-130">代わりに、スケジュールされたタスク、外部トリガー、または手動アクションを使用してビューを再生成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-130">Alternatively, consider using a scheduled task, an external trigger, or a manual action to regenerate the view.</span></span>

<span data-ttu-id="8cf2f-131">一部のシステムでは、イベント ソーシング パターンを使用して、データを変更したイベントだけを保存するストアを保持する場合などに、具体化されたビューが必要になります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-131">In some systems, such as when using the Event Sourcing pattern to maintain a store of only the events that modified the data, materialized views are necessary.</span></span> <span data-ttu-id="8cf2f-132">すべてのイベントを調べ、現在の状態を確認してビューを事前設定することが、イベント ストアから情報を取得する唯一の方法と考えられます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-132">Prepopulating views by examining all events to determine the current state might be the only way to obtain information from the event store.</span></span> <span data-ttu-id="8cf2f-133">イベント ソーシングを使用していない場合は、具体化されたビューが役立つかどうかを検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-133">If you're not using Event Sourcing, you need to consider whether a materialized view is helpful or not.</span></span> <span data-ttu-id="8cf2f-134">具体化されたビューは、1 つまたは少数のクエリに合わせて調整される傾向があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-134">Materialized views tend to be specifically tailored to one, or a small number of queries.</span></span> <span data-ttu-id="8cf2f-135">多数のクエリを使用する場合、具体化されたビューによって、ストレージ容量の許容できない要件やストレージ コストが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-135">If many queries are used, materialized views can result in unacceptable storage capacity requirements and storage cost.</span></span>

<span data-ttu-id="8cf2f-136">ビューの生成時とビューの更新時 (スケジュールに従って更新が実行される場合) のデータ整合性への影響を考慮します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-136">Consider the impact on data consistency when generating the view, and when updating the view if this occurs on a schedule.</span></span> <span data-ttu-id="8cf2f-137">ビューが生成された時点でソース データが変更されると、ビュー内のデータのコピーが元のデータと完全には一致しなくなります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-137">If the source data is changing at the point when the view is generated, the copy of the data in the view won't be fully consistent with the original data.</span></span>

<span data-ttu-id="8cf2f-138">ビューを保存する場所を検討します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-138">Consider where you'll store the view.</span></span> <span data-ttu-id="8cf2f-139">ビューは、元のデータと同じストアまたはパーティションに配置する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-139">The view doesn't have to be located in the same store or partition as the original data.</span></span> <span data-ttu-id="8cf2f-140">結合された少数の異なるパーティションのサブセットに配置できます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-140">It can be a subset from a few different partitions combined.</span></span>

<span data-ttu-id="8cf2f-141">ビューは失われても再構築できます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-141">A view can be rebuilt if lost.</span></span> <span data-ttu-id="8cf2f-142">そのため、ビューが一時的なものであり、データの現在の状態を反映してクエリのパフォーマンスを向上させたり、スケーラビリティを向上させたりするためだけに使用する場合は、キャッシュや信頼性の低い場所に保存できます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-142">Because of that, if the view is transient and is only used to improve query performance by reflecting the current state of the data, or to improve scalability, it can be stored in a cache or in a less reliable location.</span></span>

<span data-ttu-id="8cf2f-143">具体化されたビューを定義するときは、既存のデータ項目の計算や変換、クエリに渡される値、またはこれらの値の組合せ (適宜) に基づいてデータ項目や列を追加することで、価値を最大化します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-143">When defining a materialized view, maximize its value by adding data items or columns to it based on computation or transformation of existing data items, on values passed in the query, or on combinations of these values when appropriate.</span></span>

<span data-ttu-id="8cf2f-144">ストレージ メカニズムでサポートされている場合は、パフォーマンスをさらに向上させるために、具体化されたビューのインデックス作成を検討します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-144">Where the storage mechanism supports it, consider indexing the materialized view to further increase performance.</span></span> <span data-ttu-id="8cf2f-145">Apache Hadoop を基盤とするビッグ データ ソリューションと同様に、ほとんどのリレーショナルデータベースでビューのインデックス作成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-145">Most relational databases support indexing for views, as do big data solutions based on Apache Hadoop.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="8cf2f-146">このパターンを使用する状況</span><span class="sxs-lookup"><span data-stu-id="8cf2f-146">When to use this pattern</span></span>

<span data-ttu-id="8cf2f-147">このパターンは次の状況で役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-147">This pattern is useful when:</span></span>
- <span data-ttu-id="8cf2f-148">直接照会することが難しいデータの具体化されたビューを作成する場合や、正規化された方法、半構造化された方法、または構造化されていない方法で格納されたデータを抽出するためにクエリを非常に複雑にする必要がある場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-148">Creating materialized views over data that's difficult to query directly, or where queries must be very complex to extract data that's stored in a normalized, semi-structured, or unstructured way.</span></span>
- <span data-ttu-id="8cf2f-149">クエリのパフォーマンスを大幅に向上させることができる一時ビュー、またはレポートや表示用に UI のソース ビューまたはデータ転送オブジェクトとして直接機能する一時ビューを作成する場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-149">Creating temporary views that can dramatically improve query performance, or can act directly as source views or data transfer objects for the UI, for reporting, or for display.</span></span>
- <span data-ttu-id="8cf2f-150">データ ストアへの接続を常に利用できるわけではない接続または切断シナリオをサポートすることがある場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-150">Supporting occasionally connected or disconnected scenarios where connection to the data store isn't always available.</span></span> <span data-ttu-id="8cf2f-151">この場合、ビューをローカルにキャッシュできます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-151">The view can be cached locally in this case.</span></span>
- <span data-ttu-id="8cf2f-152">ソース データの形式の知識を必要としない方法で、クエリを簡素化し、実験用のデータを公開する場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-152">Simplifying queries and exposing data for experimentation in a way that doesn't require knowledge of the source data format.</span></span> <span data-ttu-id="8cf2f-153">たとえば、1 つ以上のデータベースのさまざまなテーブルや NoSQL ストアの 1 つ以上のドメインを結合して、最終的な用途に合わせてデータを書式設定する場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-153">For example, by joining different tables in one or more databases, or one or more domains in NoSQL stores, and then formatting the data to fit its eventual use.</span></span>
- <span data-ttu-id="8cf2f-154">セキュリティまたはプライバシー上の理由から、広くアクセス可能であること、変更可能であること、またはユーザーに完全に公開されることが望ましくないソース データの、特定のサブセットにアクセスできるようにする場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-154">Providing access to specific subsets of the source data that, for security or privacy reasons, shouldn't be generally accessible, open to modification, or fully exposed to users.</span></span>
- <span data-ttu-id="8cf2f-155">個々の機能を利用するために、さまざまなデータ ストアをブリッジする場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-155">Bridging different data stores, to take advantage of their individual capabilities.</span></span> <span data-ttu-id="8cf2f-156">たとえば、効率的に書き込むことができるクラウド ストアを参照データ ストアとして使用し、具体化されたビューを保持するためにクエリと読み取りのパフォーマンスに優れたリレーショナル データベースを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-156">For example, using a cloud store that's efficient for writing as the reference data store, and a relational database that offers good query and read performance to hold the materialized views.</span></span>

<span data-ttu-id="8cf2f-157">このパターンは次の状況では役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-157">This pattern isn't useful in the following situations:</span></span>
- <span data-ttu-id="8cf2f-158">ソース データが単純で簡単に照会できる場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-158">The source data is simple and easy to query.</span></span>
- <span data-ttu-id="8cf2f-159">ソース データの変更のペースが非常に速い場合や、ビューを使用しなくてもアクセスできる場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-159">The source data changes very quickly, or can be accessed without using a view.</span></span> <span data-ttu-id="8cf2f-160">このような場合、ビューを作成する際の処理オーバーヘッドを回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-160">In these cases, you should avoid the processing overhead of creating views.</span></span>
- <span data-ttu-id="8cf2f-161">整合性の優先度が高い場合。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-161">Consistency is a high priority.</span></span> <span data-ttu-id="8cf2f-162">ビューが元のデータと常に完全に一致するとは限りません。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-162">The views might not always be fully consistent with the original data.</span></span>

## <a name="example"></a><span data-ttu-id="8cf2f-163">例</span><span class="sxs-lookup"><span data-stu-id="8cf2f-163">Example</span></span>

<span data-ttu-id="8cf2f-164">次の図は、具体化されたビュー パターンを使用して売上の概要を生成する例を示しています。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-164">The following figure shows an example of using the Materialized View pattern to generate a summary of sales.</span></span> <span data-ttu-id="8cf2f-165">Azure ストレージ アカウント内の個別のパーティションにある Order、OrderItem、Customer の各テーブルのデータを結合して、Electronics カテゴリの各製品の総売上高と、各品目を購入した顧客数を含むビューを生成します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-165">Data in the Order, OrderItem, and Customer tables in separate partitions in an Azure storage account are combined to generate a view containing the total sales value for each product in the Electronics category, along with a count of the number of customers who made purchases of each item.</span></span>

![図2：具体化されたビュー パターンを使用した売上の概要の生成](./_images/materialized-view-summary-diagram.png)


<span data-ttu-id="8cf2f-167">この具体化されたビューを作成するには、複雑なクエリが必要です。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-167">Creating this materialized view requires complex queries.</span></span> <span data-ttu-id="8cf2f-168">ただし、クエリ結果を具体化されたビューとして公開することで、ユーザーは結果を簡単に取得し、直接使用したり、別のクエリに組み込んだりできます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-168">However, by exposing the query result as a materialized view, users can easily obtain the results and use them directly or incorporate them in another query.</span></span> <span data-ttu-id="8cf2f-169">このビューはレポートシステムやダッシュボードで使用される可能性が高いので、週単位などのスケジュールに従って更新できます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-169">The view is likely to be used in a reporting system or dashboard, and can be updated on a scheduled basis such as weekly.</span></span>

>  <span data-ttu-id="8cf2f-170">この例では Azure Table Storage を使用していますが、多くのリレーショナル データベース管理システムでも、具体化されたビューがネイティブでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-170">Although this example utilizes Azure table storage, many relational database management systems also provide native support for materialized views.</span></span>

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="8cf2f-171">関連のあるパターンとガイダンス</span><span class="sxs-lookup"><span data-stu-id="8cf2f-171">Related patterns and guidance</span></span>

<span data-ttu-id="8cf2f-172">このパターンを実装する場合は、次のパターンとガイダンスも関連している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-172">The following patterns and guidance might also be relevant when implementing this pattern:</span></span>
- <span data-ttu-id="8cf2f-173">[Data consistency primer (データ整合性入門)](https://msdn.microsoft.com/library/dn589800.aspx)。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-173">[Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx).</span></span> <span data-ttu-id="8cf2f-174">具体化されたビューの概要情報を管理して、基になるデータ値が反映されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-174">The summary information in a materialized view has to be maintained so that it reflects the underlying data values.</span></span> <span data-ttu-id="8cf2f-175">データ値の変更に伴って、概要データをリアルタイムで更新するのは現実的ではありません。代わりに、最終的に一貫したアプローチを採用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-175">As the data values change, it might not be practical to update the summary data in real time, and instead you'll have to adopt an eventually consistent approach.</span></span> <span data-ttu-id="8cf2f-176">分散型データの一貫性の維持にまつわる問題と、異なる一貫性モデルのメリットとトレードオフについて説明します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-176">Summarizes the issues surrounding maintaining consistency over distributed data, and describes the benefits and tradeoffs of different consistency models.</span></span>
- <span data-ttu-id="8cf2f-177">[コマンド クエリ責務分離 (CQRS) パターン](cqrs.md)。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-177">[Command and Query Responsibility Segregation (CQRS) pattern](cqrs.md).</span></span> <span data-ttu-id="8cf2f-178">基になるデータ値が変更されたときに発生するイベントに応答して具体化されたビューの情報を更新するために使用します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-178">Use to update the information in a materialized view by responding to events that occur when the underlying data values change.</span></span>
- <span data-ttu-id="8cf2f-179">[イベント ソーシング パターン](event-sourcing.md)。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-179">[Event Sourcing pattern](event-sourcing.md).</span></span> <span data-ttu-id="8cf2f-180">具体化されたビューで情報を保持するために、CQRS パターンと組み合わせて使用します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-180">Use in conjunction with the CQRS pattern to maintain the information in a materialized view.</span></span> <span data-ttu-id="8cf2f-181">具体化されたビューの基になっているデータ値が変更されたときに、システムはこれらの変更を示すイベントを発生させ、イベント ストアに保存できます。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-181">When the data values a materialized view is based on are changed, the system can raise events that describe these changes and save them in an event store.</span></span>
- <span data-ttu-id="8cf2f-182">[Index Table Pattern (インデックス テーブル パターン)](index-table.md)。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-182">[Index Table pattern](index-table.md).</span></span> <span data-ttu-id="8cf2f-183">通常、具体化されたビューのデータは主キー別に整理されていますが、クエリで他のフィールドのデータを調べて、このビューから情報を取得することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-183">The data in a materialized view is typically organized by a primary key, but queries might need to retrieve information from this view by examining data in other fields.</span></span> <span data-ttu-id="8cf2f-184">ネイティブのセカンダリ インデックスをサポートしていないデータ ストアのデータ セットのセカンダリ インデックスを作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="8cf2f-184">Use to create secondary indexes over data sets for data stores that don't support native secondary indexes.</span></span>