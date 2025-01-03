## TOC

### Infra

1. Distributed Computing Hardware Infrastructure
2. Distributed Computing Software Framework
3. Pipeline throughout clusters

### Data Handling

1. Storage

   - single machine 접근법
     - 원본파일은 zip파일로 보관
     - 데이터 정규화를 거쳐 RDBMS에 저장

   - cluster 접근법
     - `apache hadoop`의 **HDFS**를 활용

2. Processing

   - Loading & Parsing
     - `fetch` for JSON
     - `csv-parse` for CSV
     - `d3.js`도 사용 가능

   - Filtering & Transforming
     - 전체 데이터에서 필요한 데이터만 추출
     - `Array.filter`, `Array.map` 등 활용

    - Aggregating
      - 전체 데이터를 종합하여 하나의 값으로 요약
      - `Array.reduce`, `Array.forEach` 등 활용
      - Danfo.js
        > [!bl] 알아두기
        >
        > `Danfo.js`는 `pandas`의 `JavaScript` 버전이다.
        > Typescript를 완전히 지원한다.
        > Streaming을 지원한다.

        > [!rf]
        >
        > [github](https://github.com/javascriptdata/danfojs)
        > [official manual](https://danfo.jsdata.org/getting-started)

    - Visualization
      - [d3.js](https://d3js.org/)
        - 데이터 시각화 라이브러리
        - loader, parser 등도 수행

3. Data Deployment

### Optimizing

#### With Single Machine

1. Data Compression: `gzip` 등을 활용
2. streaming processing
    - `fs.createReadStream`
    - `fetch(URL).then(res => { const reader = res.body.getReader() })`
3. memory management
  : _GC가 알아서 한다지만, 데이터를 담은 (참조)변수에 직접 빈 값 등으로 덮어씌워(재할당) GC를 도울 수 있음_
4. parallel processing
  : _데이터 처리를 별도의 스레드에서 수행, 결과값을 DOM에 반영할때만 main thread 활용_

    - Transferable objects를 활용하여 zero-copy로 worker thread에 데이터 전달(소유권 이전)
    - ex) web worker
    - ex) child_process, Promise.all 조합하여 활용

#### With Cluster(Distributed Computing)

1. Data Partitioning: `apache hadoop`, `apache spark` 등을 활용
2. parallel processing: `apache hadoop`, `apache spark` 등을 활용


## 편린들

> [!ye] 데이터 형태의 차이를 구분하고, 각각에 대응할 수 있어야 한다.
>
> 배열이나 객체처럼 구조가 복잡한 데이터가 있고, flat하게 관리하기 좋은 데이터가 있다. 이에 대한 각각의 데이터 처리 인프라가 존재할 수 있겠다.
> 말자하면, json으로 표현하기 좋은 형태의 데이터 또는 그래야만 내용을 온전히 담을 수 있는 데이터를 억지로 flat하게 만드는 것이 매우 까다롭고 비효율적일 수 있다는 것이다.



## reference

- [How to Work with Big Data using JavaScript](https://www.squash.io/how-to-work-with-big-data-using-javascript/) By Nandi Lunga, Last Updated: August 14, 2023
