---
title: Azure HDInsight의 Power BI를 사용 하 여 대화형 쿼리 Hive 데이터를 참조 하세요.
description: Microsoft Power BI를 사용하여 Azure HDInsight에서 대화형 쿼리 Hive 데이터를 시각화
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 92e94c911acb701b1ccf8e39636d152cc5bfb575
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534807"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>HDInsight에서 직접 쿼리를 사용 하 여 Microsoft Power BI로 대화형 쿼리 Apache Hive 데이터 시각화

이 문서에서는 Microsoft Power BI를 Azure HDInsight 대화형 쿼리 클러스터에 연결하고 직접 쿼리를 사용하여 Apache Hive 데이터를 시각화하는 방법을 설명합니다. 제공 된 예제에서는 Hive 테이블의 데이터를 `hivesampletable` Power BI 로드 합니다. `hivesampletable`Hive 테이블은 일부 휴대폰 사용 데이터를 포함 합니다. 그런 다음 전 세계 맵에 사용량 현황 데이터를 그림으로 나타냅니다.

![HDInsight Power BI 맵 보고서](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

[Apache Hive ODBC 드라이버](../hadoop/apache-hadoop-connect-hive-power-bi.md)를 활용하여 Power BI Desktop에서 제네릭 ODBC 커넥터를 통해 가져오기를 수행할 수 있습니다. 그러나 BI 워크로드의 경우 Hive 쿼리 엔진의 비대화형 특성을 지정하지 않는 것이 좋습니다. 성능을 개선하기 위해 [HDInsight 대화형 쿼리 커넥터](./apache-hadoop-connect-hive-power-bi-directquery.md) 및 [HDInsight Apache Spark 커넥터](/power-bi/spark-on-hdinsight-with-direct-connect)를 선택할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight 클러스터** . 클러스터는 Apache Hive를 사용한 HDInsight 클러스터 또는 새로 릴리스된 대화형 쿼리 클러스터일 수 있습니다. 클러스터를 만드는 방법은 [클러스터 만들기](../hadoop/apache-hadoop-linux-tutorial-get-started.md)를 참조하세요.
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=45331)에서 복사본을 다운로드할 수 있습니다.

## <a name="load-data-from-hdinsight"></a>HDInsight에서 데이터 로드

`hivesampletable`Hive 테이블은 모든 HDInsight 클러스터와 함께 제공 됩니다.

1. Power BI Desktop을 시작합니다.

2. 메뉴 모음에서 **홈**  >  **데이터 가져오기**  >  **자세히** 로 이동 합니다.

    ![HDInsight Power BI 데이터 가져오기](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. **데이터 가져오기** 창에서 검색 상자에 **hdinsight** 를 입력 합니다.  

4. 검색 결과에서 **HDInsight 대화형 쿼리** 를 선택한 다음 **연결** 을 선택 합니다.  **HDInsight 대화형 쿼리가** 표시 되지 않는 경우 Power BI Desktop를 최신 버전으로 업데이트 해야 합니다.

5. **계속** 을 선택 하 여 **타사 서비스에 연결** 대화 상자를 닫습니다.

6. **HDInsight 대화형 쿼리** 창에서 다음 정보를 입력 한 다음 **확인** 을 선택 합니다.

    |속성 | 값 |
    |---|---|
    |서버 |클러스터 이름 (예: *myiqcluster.azurehdinsight.net* )을 입력 합니다.|
    |데이터베이스 |이 문서에 대 한 **기본값** 을 입력 합니다.|
    |데이터 연결 모드 |이 문서에 대해 **DirectQuery** 를 선택 합니다.|

    ![HDInsight 대화형 쿼리 Power BI DirectQuery 연결](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. HTTP 자격 증명을 입력 한 다음 **연결** 을 선택 합니다. 기본 사용자 이름은 **admin** 입니다.

8. 왼쪽 창의 **탐색기** 창에서 **hivesampletale** 를 선택 합니다.

9. 주 창에서 **로드** 를 선택 합니다.

    ![HDInsight 대화형 쿼리 Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>맵에 데이터 시각화

마지막 절차에서 계속 진행합니다.

1. 시각화 창에서 **지도** , 지구본 아이콘을 선택 합니다. 그러면 기본 창에 일반 지도가 표시 됩니다.

    ![HDInsight Power BI 보고서 사용자 지정](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. 필드 창에서 **country** 및 **devicemake** 를 선택합니다. 몇 분 후에 데이터 요소가 있는 세계 지도가 주 창에 표시 됩니다.

3. 맵을 확장합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Power BI를 사용하여 HDInsight에서 데이터를 시각화하는 방법을 알아보았습니다.  데이터 시각화에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight에서 ODBC를 사용 하 여 Microsoft Power BI를 사용 하 여 Apache Hive 데이터를 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md)합니다. 
* [Apache Zeppelin를 사용 하 여 Azure HDInsight에서 Apache Hive 쿼리를 실행](../interactive-query/hdinsight-connect-hive-zeppelin.md)합니다.
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [파워 쿼리를 사용 하 여 Apache Hadoop에 Excel을 연결](../hadoop/apache-hadoop-connect-excel-power-query.md)합니다.
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Visual Studio Code에 대해 Azure HDInsight 도구를 사용](../hdinsight-for-vscode.md)합니다.
* [HDInsight에 데이터를 업로드](./../hdinsight-upload-data.md)합니다.