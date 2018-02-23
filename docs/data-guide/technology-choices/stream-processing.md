---
title: "ストリーム処理テクノロジの選択"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: e06f46e2951159219bd8cc430102e2ec0c5d6d4d
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-stream-processing-technology-in-azure"></a><span data-ttu-id="21fe1-102">Azure でのストリーム処理テクノロジの選択</span><span class="sxs-lookup"><span data-stu-id="21fe1-102">Choosing a stream processing technology in Azure</span></span>

<span data-ttu-id="21fe1-103">この記事では、Azure でのリアルタイム ストリーミング処理を行うためのテクノロジの選択肢を比較します。</span><span class="sxs-lookup"><span data-stu-id="21fe1-103">This article compares technology choices for real-time stream processing in Azure.</span></span>

<span data-ttu-id="21fe1-104">リアルタイム ストリーム処理では、キューまたはファイル ベースのストレージのメッセージを使用し、メッセージを処理し、結果を別のメッセージ キュー、ファイル ストア、またはデータベースに転送します。</span><span class="sxs-lookup"><span data-stu-id="21fe1-104">Real-time stream processing consumes messages from either queue or file-based storage, process the messages, and forward the result to another message queue, file store, or database.</span></span> <span data-ttu-id="21fe1-105">処理には、クエリの実行、フィルター処理、およびメッセージの集計が含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="21fe1-105">Processing may include querying, filtering, and aggregating messages.</span></span> <span data-ttu-id="21fe1-106">ストリーム処理エンジンは、終わりのないデータのストリームを使用して、最小限の待機時間で結果を生成できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="21fe1-106">Stream processing engines must be able to consume an endless streams of data and produce results with minimal latency.</span></span> <span data-ttu-id="21fe1-107">詳しくは、「[Real time processing](../scenarios/real-time-processing.md)」(リアルタイム処理) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="21fe1-107">For more information, see [Real time processing](../scenarios/real-time-processing.md).</span></span>

## <a name="what-are-your-options-when-choosing-a-technology-for-real-time-processing"></a><span data-ttu-id="21fe1-108">リアルタイム処理用のテクノロジを選択する場合の選択肢</span><span class="sxs-lookup"><span data-stu-id="21fe1-108">What are your options when choosing a technology for real-time processing?</span></span>
<span data-ttu-id="21fe1-109">Azure では、以下のすべてのデータ ストアがリアルタイム処理のコア要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="21fe1-109">In Azure, all of the following data stores will meet the core requirements supporting real-time processing:</span></span>
- [<span data-ttu-id="21fe1-110">Azure Stream Analytics</span><span class="sxs-lookup"><span data-stu-id="21fe1-110">Azure Stream Analytics</span></span>](/azure/stream-analytics/)
- [<span data-ttu-id="21fe1-111">Spark Streaming を使用する HDInsight</span><span class="sxs-lookup"><span data-stu-id="21fe1-111">HDInsight with Spark Streaming</span></span>](/azure/hdinsight/spark/apache-spark-streaming-overview)
- [<span data-ttu-id="21fe1-112">Storm を使用する HDInsight</span><span class="sxs-lookup"><span data-stu-id="21fe1-112">HDInsight with Storm</span></span>](/azure/hdinsight/storm/apache-storm-overview)
- [<span data-ttu-id="21fe1-113">Azure Functions</span><span class="sxs-lookup"><span data-stu-id="21fe1-113">Azure Functions</span></span>](/azure/azure-functions/functions-overview)
- [<span data-ttu-id="21fe1-114">Azure App Service WebJobs</span><span class="sxs-lookup"><span data-stu-id="21fe1-114">Azure App Service WebJobs</span></span>](/azure/app-service/web-sites-create-web-jobs)

## <a name="key-selection-criteria"></a><span data-ttu-id="21fe1-115">主要な選択条件</span><span class="sxs-lookup"><span data-stu-id="21fe1-115">Key Selection Criteria</span></span>

<span data-ttu-id="21fe1-116">リアルタイム処理のシナリオでは、次の質問に答えることによって、ニーズに適した適切なサービスを選択することから始めます。</span><span class="sxs-lookup"><span data-stu-id="21fe1-116">For real-time processing scenarios, begin choosing the appropriate service for your needs by answering these questions:</span></span>

- <span data-ttu-id="21fe1-117">宣言型または命令型のアプローチでストリーム処理ロジックを作成したいですか。</span><span class="sxs-lookup"><span data-stu-id="21fe1-117">Do you prefer a declarative or imperative approach to authoring stream processing logic?</span></span>

- <span data-ttu-id="21fe1-118">テンポラル処理またはウィンドウに対する組み込みのサポートが必要ですか。</span><span class="sxs-lookup"><span data-stu-id="21fe1-118">Do you need built-in support for temporal processing or windowing?</span></span>

- <span data-ttu-id="21fe1-119">データは、Avro、JSON、または CSV 以外の形式で到着しますか。</span><span class="sxs-lookup"><span data-stu-id="21fe1-119">Does your data arrive in formats besides Avro, JSON, or CSV?</span></span> <span data-ttu-id="21fe1-120">「はい」の場合は、カスタム コードを使用して任意の形式をサポートするオプションを検討してください。</span><span class="sxs-lookup"><span data-stu-id="21fe1-120">If yes, consider options support any format using custom code.</span></span>

- <span data-ttu-id="21fe1-121">1 GB/秒を超えるように処理を拡張する必要がありますか。</span><span class="sxs-lookup"><span data-stu-id="21fe1-121">Do you need to scale your processing beyond 1 GB/s?</span></span> <span data-ttu-id="21fe1-122">「はい」の場合は、クラスター サイズに対応するオプションを検討してください。</span><span class="sxs-lookup"><span data-stu-id="21fe1-122">If yes, consider the options that scale with the cluster size.</span></span> 

## <a name="capability-matrix"></a><span data-ttu-id="21fe1-123">機能のマトリックス</span><span class="sxs-lookup"><span data-stu-id="21fe1-123">Capability matrix</span></span>

<span data-ttu-id="21fe1-124">次の表は、機能の主な相違点をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="21fe1-124">The following tables summarize the key differences in capabilities.</span></span> 

### <a name="general-capabilities"></a><span data-ttu-id="21fe1-125">一般的な機能</span><span class="sxs-lookup"><span data-stu-id="21fe1-125">General capabilities</span></span>
| | <span data-ttu-id="21fe1-126">Azure Stream Analytics</span><span class="sxs-lookup"><span data-stu-id="21fe1-126">Azure Stream Analytics</span></span> | <span data-ttu-id="21fe1-127">Spark Streaming を使用する HDInsight</span><span class="sxs-lookup"><span data-stu-id="21fe1-127">HDInsight with Spark Streaming</span></span> | <span data-ttu-id="21fe1-128">Storm を使用する HDInsight</span><span class="sxs-lookup"><span data-stu-id="21fe1-128">HDInsight with Storm</span></span> | <span data-ttu-id="21fe1-129">Azure Functions</span><span class="sxs-lookup"><span data-stu-id="21fe1-129">Azure Functions</span></span> | <span data-ttu-id="21fe1-130">Azure App Service WebJobs</span><span class="sxs-lookup"><span data-stu-id="21fe1-130">Azure App Service WebJobs</span></span> |
| --- | --- | --- | --- | --- | --- | 
| <span data-ttu-id="21fe1-131">プログラミング</span><span class="sxs-lookup"><span data-stu-id="21fe1-131">Programmability</span></span> | <span data-ttu-id="21fe1-132">ストリーム分析クエリ言語、JavaScript</span><span class="sxs-lookup"><span data-stu-id="21fe1-132">Stream analytics query language, JavaScript</span></span> | <span data-ttu-id="21fe1-133">Scala、Python、Java</span><span class="sxs-lookup"><span data-stu-id="21fe1-133">Scala, Python, Java</span></span> | <span data-ttu-id="21fe1-134">Java、C#</span><span class="sxs-lookup"><span data-stu-id="21fe1-134">Java, C#</span></span> | <span data-ttu-id="21fe1-135">C#、F#、Node.js</span><span class="sxs-lookup"><span data-stu-id="21fe1-135">C#, F#, Node.js</span></span> | <span data-ttu-id="21fe1-136">C#、Node.js、PHP、Java、Python</span><span class="sxs-lookup"><span data-stu-id="21fe1-136">C#, Node.js, PHP, Java, Python</span></span> |
| <span data-ttu-id="21fe1-137">プログラミング パラダイム</span><span class="sxs-lookup"><span data-stu-id="21fe1-137">Programming paradigm</span></span> | <span data-ttu-id="21fe1-138">宣言型</span><span class="sxs-lookup"><span data-stu-id="21fe1-138">Declarative</span></span> | <span data-ttu-id="21fe1-139">宣言型と命令型の混合</span><span class="sxs-lookup"><span data-stu-id="21fe1-139">Mixture of declarative and imperative</span></span> | <span data-ttu-id="21fe1-140">命令型</span><span class="sxs-lookup"><span data-stu-id="21fe1-140">Imperative</span></span> | <span data-ttu-id="21fe1-141">命令型</span><span class="sxs-lookup"><span data-stu-id="21fe1-141">Imperative</span></span> | <span data-ttu-id="21fe1-142">命令型</span><span class="sxs-lookup"><span data-stu-id="21fe1-142">Imperative</span></span> |    
| <span data-ttu-id="21fe1-143">価格モデル</span><span class="sxs-lookup"><span data-stu-id="21fe1-143">Pricing model</span></span> | <span data-ttu-id="21fe1-144">ストリーミング ユニット単位</span><span class="sxs-lookup"><span data-stu-id="21fe1-144">By streaming units</span></span> | <span data-ttu-id="21fe1-145">クラスター時間単位</span><span class="sxs-lookup"><span data-stu-id="21fe1-145">By cluster hour</span></span> | <span data-ttu-id="21fe1-146">クラスター時間単位</span><span class="sxs-lookup"><span data-stu-id="21fe1-146">By cluster hour</span></span> | <span data-ttu-id="21fe1-147">関数の実行とリソースの消費量あたり</span><span class="sxs-lookup"><span data-stu-id="21fe1-147">Per function execution and resource consumption</span></span> | <span data-ttu-id="21fe1-148">App Service プランの時間単位</span><span class="sxs-lookup"><span data-stu-id="21fe1-148">Per app service plan hour</span></span> |  

### <a name="integration-capabilities"></a><span data-ttu-id="21fe1-149">統合機能</span><span class="sxs-lookup"><span data-stu-id="21fe1-149">Integration capabilities</span></span>
| | <span data-ttu-id="21fe1-150">Azure Stream Analytics</span><span class="sxs-lookup"><span data-stu-id="21fe1-150">Azure Stream Analytics</span></span> | <span data-ttu-id="21fe1-151">Spark Streaming を使用する HDInsight</span><span class="sxs-lookup"><span data-stu-id="21fe1-151">HDInsight with Spark Streaming</span></span> | <span data-ttu-id="21fe1-152">Storm を使用する HDInsight</span><span class="sxs-lookup"><span data-stu-id="21fe1-152">HDInsight with Storm</span></span> | <span data-ttu-id="21fe1-153">Azure Functions</span><span class="sxs-lookup"><span data-stu-id="21fe1-153">Azure Functions</span></span> | <span data-ttu-id="21fe1-154">Azure App Service WebJobs</span><span class="sxs-lookup"><span data-stu-id="21fe1-154">Azure App Service WebJobs</span></span> |
| --- | --- | --- | --- | --- | --- | 
| <span data-ttu-id="21fe1-155">入力</span><span class="sxs-lookup"><span data-stu-id="21fe1-155">Inputs</span></span> | [<span data-ttu-id="21fe1-156">Stream Analytics の入力</span><span class="sxs-lookup"><span data-stu-id="21fe1-156">Stream Analytics inputs</span></span>](/azure/stream-analytics/stream-analytics-define-inputs)  | <span data-ttu-id="21fe1-157">Event Hubs、IoT Hub、Kafka、HDFS</span><span class="sxs-lookup"><span data-stu-id="21fe1-157">Event Hubs, IoT Hub, Kafka, HDFS</span></span>  | <span data-ttu-id="21fe1-158">Event Hubs、IoT Hub、Storage Blobs、Azure Data Lake Store</span><span class="sxs-lookup"><span data-stu-id="21fe1-158">Event Hubs, IoT Hub, Storage Blobs, Azure Data Lake Store</span></span>  | [<span data-ttu-id="21fe1-159">サポートされるバインディング</span><span class="sxs-lookup"><span data-stu-id="21fe1-159">Supported bindings</span></span>](/azure/azure-functions/functions-triggers-bindings#supported-bindings) | <span data-ttu-id="21fe1-160">Service Bus、Storage Queues、Storage Blobs、Event Hubs、WebHooks、Cosmos DB、Files</span><span class="sxs-lookup"><span data-stu-id="21fe1-160">Service Bus, Storage Queues, Storage Blobs, Event Hubs, WebHooks, Cosmos DB, Files</span></span> |
| <span data-ttu-id="21fe1-161">シンク</span><span class="sxs-lookup"><span data-stu-id="21fe1-161">Sinks</span></span> |  [<span data-ttu-id="21fe1-162">Stream Analytics の出力</span><span class="sxs-lookup"><span data-stu-id="21fe1-162">Stream Analytics outputs</span></span>](/azure/stream-analytics/stream-analytics-define-outputs) | <span data-ttu-id="21fe1-163">HDFS</span><span class="sxs-lookup"><span data-stu-id="21fe1-163">HDFS</span></span> | <span data-ttu-id="21fe1-164">Event Hubs、Service Bus、Kafka</span><span class="sxs-lookup"><span data-stu-id="21fe1-164">Event Hubs, Service Bus, Kafka</span></span> | [<span data-ttu-id="21fe1-165">サポートされるバインディング</span><span class="sxs-lookup"><span data-stu-id="21fe1-165">Supported bindings</span></span>](/azure/azure-functions/functions-triggers-bindings#supported-bindings) | <span data-ttu-id="21fe1-166">Service Bus、Storage Queues、Storage Blobs、Event Hubs、WebHooks、Cosmos DB、Files</span><span class="sxs-lookup"><span data-stu-id="21fe1-166">Service Bus, Storage Queues, Storage Blobs, Event Hubs, WebHooks, Cosmos DB, Files</span></span> | 

### <a name="processing-capabilities"></a><span data-ttu-id="21fe1-167">処理機能</span><span class="sxs-lookup"><span data-stu-id="21fe1-167">Processing capabilities</span></span>
| | <span data-ttu-id="21fe1-168">Azure Stream Analytics</span><span class="sxs-lookup"><span data-stu-id="21fe1-168">Azure Stream Analytics</span></span> | <span data-ttu-id="21fe1-169">Spark Streaming を使用する HDInsight</span><span class="sxs-lookup"><span data-stu-id="21fe1-169">HDInsight with Spark Streaming</span></span> | <span data-ttu-id="21fe1-170">Storm を使用する HDInsight</span><span class="sxs-lookup"><span data-stu-id="21fe1-170">HDInsight with Storm</span></span> | <span data-ttu-id="21fe1-171">Azure Functions</span><span class="sxs-lookup"><span data-stu-id="21fe1-171">Azure Functions</span></span> | <span data-ttu-id="21fe1-172">Azure App Service WebJobs</span><span class="sxs-lookup"><span data-stu-id="21fe1-172">Azure App Service WebJobs</span></span> |
| --- | --- | --- | --- | --- | --- | 
| <span data-ttu-id="21fe1-173">組み込みのテンポラル/ウィンドウの サポート</span><span class="sxs-lookup"><span data-stu-id="21fe1-173">Built-in temporal/windowing support</span></span> | <span data-ttu-id="21fe1-174">はい</span><span class="sxs-lookup"><span data-stu-id="21fe1-174">Yes</span></span> | <span data-ttu-id="21fe1-175">はい</span><span class="sxs-lookup"><span data-stu-id="21fe1-175">Yes</span></span> | <span data-ttu-id="21fe1-176">はい</span><span class="sxs-lookup"><span data-stu-id="21fe1-176">Yes</span></span> | <span data-ttu-id="21fe1-177">いいえ </span><span class="sxs-lookup"><span data-stu-id="21fe1-177">No</span></span> | <span data-ttu-id="21fe1-178">いいえ </span><span class="sxs-lookup"><span data-stu-id="21fe1-178">No</span></span> |
| <span data-ttu-id="21fe1-179">入力データ形式</span><span class="sxs-lookup"><span data-stu-id="21fe1-179">Input data formats</span></span> | <span data-ttu-id="21fe1-180">Avro、JSON または CSV、UTF-8 エンコード</span><span class="sxs-lookup"><span data-stu-id="21fe1-180">Avro, JSON or CSV, UTF-8 encoded</span></span> | <span data-ttu-id="21fe1-181">カスタム コードを使用する任意の形式</span><span class="sxs-lookup"><span data-stu-id="21fe1-181">Any format using custom code</span></span> | <span data-ttu-id="21fe1-182">カスタム コードを使用する任意の形式</span><span class="sxs-lookup"><span data-stu-id="21fe1-182">Any format using custom code</span></span> | <span data-ttu-id="21fe1-183">カスタム コードを使用する任意の形式</span><span class="sxs-lookup"><span data-stu-id="21fe1-183">Any format using custom code</span></span> | <span data-ttu-id="21fe1-184">カスタム コードを使用する任意の形式</span><span class="sxs-lookup"><span data-stu-id="21fe1-184">Any format using custom code</span></span> |
| <span data-ttu-id="21fe1-185">スケーラビリティ</span><span class="sxs-lookup"><span data-stu-id="21fe1-185">Scalability</span></span> | [<span data-ttu-id="21fe1-186">クエリ パーティション</span><span class="sxs-lookup"><span data-stu-id="21fe1-186">Query partitions</span></span>](/azure/stream-analytics/stream-analytics-parallelization) | <span data-ttu-id="21fe1-187">クラスターのサイズによる制限</span><span class="sxs-lookup"><span data-stu-id="21fe1-187">Bounded by cluster size</span></span> | <span data-ttu-id="21fe1-188">クラスターのサイズによる制限</span><span class="sxs-lookup"><span data-stu-id="21fe1-188">Bounded by cluster size</span></span> | <span data-ttu-id="21fe1-189">並列処理される最大 200 の関数アプリ インスタンス</span><span class="sxs-lookup"><span data-stu-id="21fe1-189">Up to 200 function app instances processing in parallel</span></span> | <span data-ttu-id="21fe1-190">アプリ サービス プランの容量による制限</span><span class="sxs-lookup"><span data-stu-id="21fe1-190">Bounded by app service plan capacity</span></span> | 
| <span data-ttu-id="21fe1-191">遅延着信と順不同のイベントの処理</span><span class="sxs-lookup"><span data-stu-id="21fe1-191">Late arrival and out of order event handling support</span></span> | <span data-ttu-id="21fe1-192">はい</span><span class="sxs-lookup"><span data-stu-id="21fe1-192">Yes</span></span> | <span data-ttu-id="21fe1-193">はい</span><span class="sxs-lookup"><span data-stu-id="21fe1-193">Yes</span></span> | <span data-ttu-id="21fe1-194">はい</span><span class="sxs-lookup"><span data-stu-id="21fe1-194">Yes</span></span> | <span data-ttu-id="21fe1-195">いいえ </span><span class="sxs-lookup"><span data-stu-id="21fe1-195">No</span></span> | <span data-ttu-id="21fe1-196">いいえ </span><span class="sxs-lookup"><span data-stu-id="21fe1-196">No</span></span> |

<span data-ttu-id="21fe1-197">関連項目:</span><span class="sxs-lookup"><span data-stu-id="21fe1-197">See also:</span></span>

- [<span data-ttu-id="21fe1-198">リアルタイム メッセージ取り込みテクノロジの選択</span><span class="sxs-lookup"><span data-stu-id="21fe1-198">Choosing a real-time message ingestion technology</span></span>](./real-time-ingestion.md)
- [<span data-ttu-id="21fe1-199">Apache Storm と Azure Stream Analytics の比較</span><span class="sxs-lookup"><span data-stu-id="21fe1-199">Comparing Apache Storm and Azure Stream Analytics</span></span>](/azure/stream-analytics/stream-analytics-comparison-storm)
- [<span data-ttu-id="21fe1-200">リアルタイム処理</span><span class="sxs-lookup"><span data-stu-id="21fe1-200">Real time processing</span></span>](../scenarios/real-time-processing.md)