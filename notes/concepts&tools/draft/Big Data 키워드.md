# Big Data with JavaScript

## keyworks

    - MapReduce: _빅데이터를 다루는 일종의 패러다임_

        - Basics: 데이터 프로세싱을 2단계로 나눔

            1. map: input data를 받아서 key-value pair로 변환
            2. reduce: 전단계에서 생성된 key-value pair를 받아서 목표한 값을 계산
            * filter, reduce, map 등의 메소드를 사용하여 구현

        - Scaling with Distributed Systems: 분산 컴퓨팅 시스템을 활용하여 효율 높임

            - (분산 시스템을 구축하는) 프레임워크가 필요
                - ex) `Apache Hadoop` 또는 `Apache Spark`
            - (분산 시스템 구축 이후) js의 관련 패키지를 사용해 시스템에 작업 전달

    - Apache Hadoop & Apache Spark: _분산 시스템 프레임워크_

        - HDFS(Hadoop Distributed File System)
          : 대용량 데이터를 저장하기 위한 파일 시스템

        - [hadoop vs spark](https://aws.amazon.com/ko/compare/the-difference-between-hadoop-vs-spark/)

    - apache arrow: _big data processing에 사용되는 데이터의 구조를 표준화_

        - 다양한 언어에서 사용 가능
        - in-memory data format으로 하드디스크에 읽고 쓸 필요가 없어 엄청 빠름

    - **batch** processing vs **streaming** processing

        | 구분      | 설명                              | 주요 도구           |
        |-----------|-----------------------------------|---------------------|
        | batch     | 이미 저장된 데이터를 처리         | HDFS + apache spark |
        | streaming | 실시간으로 수집되는 데이터를 처리 | kafka               |


%% TODO:
%% - hadoop
%% - kafka
%% - parquet
%% - big data pipeline
%% - pandas
