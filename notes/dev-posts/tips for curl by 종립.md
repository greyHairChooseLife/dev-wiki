_블로그에서 퍼왔다._

```html
<!DOCTYPE html>
<html>
    <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <!-- 참고: https://jekyllrb.com/docs/liquid/filters/ -->
    <title>curl 명령어 - 기계인간 John Grib</title>

    <meta name="description" content="transfer a URL">
    <meta name="google-site-verification" content="xD2UvBTOH6xxBi9MseahHz4Nt9u2vYZbgY2wVo7Bdyc" />

    <link rel="stylesheet" href="/css/main.css">
    <link rel="canonical" href="https://johngrib.github.io/wiki/cmd/curl/">
    <link rel="alternate" type="application/rss+xml" title="기계인간 John Grib" href="https://johngrib.github.io/feed.xml" />

    <meta property="og:type" content="website">
    <meta property="og:title" content="curl 명령어">
    <meta property="og:description" content="transfer a URL">
    <meta property="og:image" content="https://johngrib.github.io/resource/johngrib.png">
    <meta property="og:url" content="https://johngrib.github.io/wiki/cmd/curl/">

    <meta name="twitter:card" content="summary" />
    <meta name="twitter:site" content="@John_Grib" />
    <meta name="twitter:url" content="https://johngrib.github.io/wiki/cmd/curl/" />
    <meta name="twitter:title" content="curl 명령어" />
    <meta name="twitter:description" content="transfer a URL" />

    <link rel="apple-touch-icon" sizes="57x57"        href="/resource/icon/apple-icon-57x57.png">
    <link rel="apple-touch-icon" sizes="60x60"        href="/resource/icon/apple-icon-60x60.png">
    <link rel="apple-touch-icon" sizes="72x72"        href="/resource/icon/apple-icon-72x72.png">
    <link rel="apple-touch-icon" sizes="76x76"        href="/resource/icon/apple-icon-76x76.png">
    <link rel="apple-touch-icon" sizes="114x114"      href="/resource/icon/apple-icon-114x114.png">
    <link rel="apple-touch-icon" sizes="120x120"      href="/resource/icon/apple-icon-120x120.png">
    <link rel="apple-touch-icon" sizes="144x144"      href="/resource/icon/apple-icon-144x144.png">
    <link rel="apple-touch-icon" sizes="152x152"      href="/resource/icon/apple-icon-152x152.png">
    <link rel="apple-touch-icon" sizes="180x180"      href="/resource/icon/apple-icon-180x180.png">
    <link rel="icon" type="image/png" sizes="192x192" href="/resource/icon/android-icon-192x192.png">
    <link rel="icon" type="image/png" sizes="32x32"   href="/resource/icon/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="96x96"   href="/resource/icon/favicon-96x96.png">
    <link rel="icon" type="image/png" sizes="16x16"   href="/resource/icon/favicon-16x16.png">
    <link rel="manifest" href="/resource/icon/manifest.json">
    <meta name="msapplication-TileColor" content="#ffffff">
    <meta name="msapplication-TileImage" content="/resource/icon/ms-icon-144x144.png">
    <meta name="theme-color" content="#ffffff">


    <!-- Global site tag (gtag.js) - Google Analytics -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=G-RK3M0SSQ6G"></script>
    <script>
        // https://gist.github.com/chrisveness/43bcda93af9f646d083fad678071b90a
        async function aesGcmDecrypt(ciphertext, password) {
            const pwUtf8 = new TextEncoder().encode(password);
            const pwHash = await crypto.subtle.digest('SHA-256', pwUtf8);
            const iv = ciphertext.slice(0,24).match(/.{2}/g).map(byte => parseInt(byte, 16));
            const alg = { name: 'AES-GCM', iv: new Uint8Array(iv) };
            const key = await crypto.subtle.importKey('raw', pwHash, alg, false, ['decrypt']);
            const ctStr = atob(ciphertext.slice(24));
            const ctUint8 = new Uint8Array(ctStr.match(/[\s\S]/g).map(ch => ch.charCodeAt(0)));
            const plainBuffer = await crypto.subtle.decrypt(alg, key, ctUint8);
            const plaintext = new TextDecoder().decode(plainBuffer);
            return plaintext;
        }
        async function aesGcmEncrypt(plaintext, password) {
            const pwUtf8 = new TextEncoder().encode(password);
            const pwHash = await crypto.subtle.digest('SHA-256', pwUtf8);
            const iv = crypto.getRandomValues(new Uint8Array(12));
            const alg = { name: 'AES-GCM', iv: iv };
            const key = await crypto.subtle.importKey('raw', pwHash, alg, false, ['encrypt']);
            const ptUint8 = new TextEncoder().encode(plaintext);
            const ctBuffer = await crypto.subtle.encrypt(alg, key, ptUint8);
            const ctArray = Array.from(new Uint8Array(ctBuffer));
            const ctStr = ctArray.map(byte => String.fromCharCode(byte)).join('');
            const ctBase64 = btoa(ctStr);
            const ivHex = Array.from(iv).map(b => ('00' + b.toString(16)).slice(-2)).join('');
            return ivHex+ctBase64;
        }

        aesGcmDecrypt('a340944fd5217854388641995TPB9dYcVujRQ/O5sZRMxpoE+elmvBntTYgEIg==', 'https://johngrib.github.io')
            .then(function(ua) {
                window.dataLayer = window.dataLayer || [];
                function gtag(){dataLayer.push(arguments);}
                gtag('js', new Date());
                gtag('config', ua);
                console.log('The gtag config has been finished.');
            })
            .catch(function(error) {
                console.log(error);
                console.log('The gtag id is invalid.');
            });
    </script>


</head>
<header class="header">
    <div>
        <a class="site-title" href="/">기계인간 John Grib</a>
    </div>
    <div>
        <a class="site-title-right" href="/about/">me</a>
    </div>
    <div>
        <a id="random-button" class="site-title-right">random</a>
    </div>
    <div>
        <a class="site-title-right" href="/wiki/root-index/">index</a>
    </div>
</header>
<script async src="/js/shortcut.js"></script>

    <body>
        <div class="page-content">
            <div class="search">
    <form role="search" method="get" action="/search/">
         <input name="searchString" class="searchInput" placeholder=" 검색하세요" type="text">
         <input type="submit" class="searchButton" value="Search">
    </form>
</div>
            <div class="post">

<input type="hidden" id="thisName" value="cmd/curl"/>
<div class="post">
    <header class="post-header">
        <h1 class="page-title">
            <a href="/wiki/cmd/curl/"> curl 명령어 </a>
        </h1>

        <p class="title-summary">transfer a URL</p>

        <div class="history-button">
            <p><a href="https://github.com/johngrib/johngrib.github.io/commits/master/_wiki/cmd/curl.md" target="_blank">created: 2019.06.12</a></p>
            <p><a href="https://github.com/johngrib/johngrib.github.io/commits/master/_wiki/cmd/curl.md" target="_blank">updated: 2024.07.17</a></p>
            <p>
                <a href="https://github.com/johngrib/johngrib.github.io/blame/master/_wiki/cmd/curl.md" target="_blank">blame 보기</a>
                / <a href="https://github.com/johngrib/johngrib.github.io/edit/master/_wiki/cmd/curl.md">편집하기</a>
                / <a href="https://github.com/johngrib/johngrib.github.io/issues/new?title=curl+%EB%AA%85%EB%A0%B9%EC%96%B4&body=의견을%20남겨주세요">의견 남기기</a>
            </p>
        </div>

        <div id="parent-list"></div>



    <div class="post-tag">
    bash command
</div>




    </header>
    <article class="post-content">
        <ul id="markdown-toc">
  <li><a href="#역사" id="markdown-toc-역사">역사</a></li>
  <li><a href="#examples" id="markdown-toc-examples">Examples</a>    <ul>
      <li><a href="#다운로드" id="markdown-toc-다운로드">다운로드</a></li>
      <li><a href="#재미있는-사용법" id="markdown-toc-재미있는-사용법">재미있는 사용법</a></li>
    </ul>
  </li>
  <li><a href="#options" id="markdown-toc-options">Options</a>    <ul>
      <li><a href="#option-h" id="markdown-toc-option-h">-H : header</a>        <ul>
          <li><a href="#option-user-agent" id="markdown-toc-option-user-agent">User-Agent를 지정하는 두 가지 방법</a></li>
        </ul>
      </li>
      <li><a href="#option-o" id="markdown-toc-option-o">-o</a></li>
      <li><a href="#option-data" id="markdown-toc-option-data">--data</a></li>
      <li><a href="#option-json" id="markdown-toc-option-json">--json</a></li>
      <li><a href="#-i--response-header-출력" id="markdown-toc--i--response-header-출력">-i : response header 출력</a></li>
      <li><a href="#http-버전-지정" id="markdown-toc-http-버전-지정">http 버전 지정</a></li>
      <li><a href="#그-외-기타-옵션들" id="markdown-toc-그-외-기타-옵션들">그 외 기타 옵션들</a></li>
    </ul>
  </li>
  <li><a href="#참고문헌" id="markdown-toc-참고문헌">참고문헌</a></li>
</ul>

<h2 id="역사">역사</h2>

<ul>
  <li>1996-11-11 - Rafael Sagula에 의해 개발된 httpget
    <ul>
      <li>httpget 0.1 버전은 C 언어로 작성된 300줄 미만의 프로그램이었음</li>
      <li>Daniel Stenberg가 발견하고 기여하기 시작</li>
      <li>비슷한 시기(1996-11)에 wget의 최초 릴리즈(1.4.0)도 있었음</li>
    </ul>
  </li>
  <li>2022-02-02 - <a href="https://daniel.haxx.se/blog/2022/02/02/curl-dash-dash-json/"><code class="language-plaintext highlighter-rouge">--json</code> 옵션 추가</a></li>
</ul>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">This day 23 years ago, I uploaded the first ever curl release. Happy birthday to all of us who use and appreciate curl. I love you all.<a href="https://t.co/sbw4Yps3s6">https://t.co/sbw4Yps3s6</a> <a href="https://t.co/4VXtdiuP5b">pic.twitter.com/4VXtdiuP5b</a></p>&mdash; Daniel Stenberg (@bagder) <a href="https://twitter.com/bagder/status/1373047807876153349?ref_src=twsrc%5Etfw">March 19, 2021</a></blockquote>
<script async="" src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote>
  <p>curl's official birthday was March 20, 1998. That was the day the first ever tarball was made available that could build a tool named curl. I put it together and I called it curl 4.0 since I kept the version numbering from the previous names I had used for the tool. Or rather, I bumped it up from 3.12 which was the last version I used under the previous name: urlget.</p>

  <p>– <a href="https://daniel.haxx.se/blog/2021/03/20/curl-is-23-years-old-today/">CURL IS 23 YEARS OLD TODAY</a></p>

  <p>curl의 공식 생일은 1998년 3월 20일 이었습니다. curl이라는 도구를 빌드할 수 있는 최초의 tarball을 만든 날이었습니다. 먼저 사용하던 도구에서 버전 번호를 이어받아서 curl 4.0 이라고 불렀습니다. 좀 더 정확하게 말하자면 이전 이름인 urlget의 마지막 버전인 3.12에서 올렸다고 할 수 있습니다.</p>

  <p>– <a href="https://daniel.haxx.se/blog/2021/03/20/curl-is-23-years-old-today/">curl 23주년을 기념하는 Daniel Stenberg의 포스트 (2021-03-20)</a></p>
</blockquote>

<ul>
  <li>1998년 봄에 이름을 curl로 변경</li>
  <li>1998년 3월 20일, curl 4.0 배포</li>
</ul>

<h2 id="examples">Examples</h2>
<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="c"># 요청 보내기</span>
curl http://httpbin.org/ip

 <span class="c"># 요청시 자세한 정보 표시</span>
curl <span class="nt">-v</span> http://httpbin.org/ip
</code></pre></div></div>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="c"># POST 요청 보내기</span>
curl <span class="nt">-X</span> POST http://httpbin.org/anything
curl <span class="nt">-X</span> POST <span class="nt">--data</span> <span class="s2">"name=John&amp;age=29"</span> http://httpbin.org/anything

 <span class="c"># csv 파일을 Form(POST)으로 전송</span>
curl <span class="nt">-F</span> <span class="nv">csv</span><span class="o">=</span>@<span class="nv">$PWD</span>/sample.csv http://localhost:8088/submit-csv
</code></pre></div></div>

<h3 id="다운로드">다운로드</h3>
<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="c"># 나의 ip 정보를 담은 json 문자열을 받아 파일로 저장한다</span>
curl <span class="nt">-o</span> my_ip.json http://httpbin.org/ip
</code></pre></div></div>

<h3 id="재미있는-사용법">재미있는 사용법</h3>
<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>curl wttr.in          <span class="c"># 날씨를 본다</span>
curl v2.wttr.in
curl v2.wttr.in/Seoul

curl ifconfig.me  <span class="c"># ip주소를 본다</span>
</code></pre></div></div>

<h2 id="options">Options</h2>

<h3 id="option-h">-H : header</h3>

<p><code class="language-plaintext highlighter-rouge">-H</code>로 헤더를 지정할 수 있다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>curl <span class="se">\</span>
  <span class="nt">-H</span> <span class="s1">'Content-Type: text/html; charset=UTF=8'</span> <span class="se">\</span>
  <span class="nt">-H</span> <span class="s1">'Location: http://www.google.com'</span> https://httpbin.org/ip <span class="se">\</span>
  http://httpbin.org/ip
</code></pre></div></div>

<p>위와 같이 명령을 입력하면 아래와 같은 Http Request가 전송된다.</p>

<div class="language-text highlighter-rouge"><div class="highlight"><pre class="highlight"><code>GET /ip HTTP/1.1
Host: httpbin.org
User-Agent: curl/7.54.0
Accept: */*
Content-Type: text/html; charset=UTF=8
Location: http://xxx...
</code></pre></div></div>

<h4 id="option-user-agent">User-Agent를 지정하는 두 가지 방법</h4>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>curl <span class="se">\</span>
  <span class="nt">-H</span> <span class="s2">"User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X x.y; rv:42.0) Gecko/20100101 Firefox/42.0"</span> <span class="se">\</span>
  http://localhost:8080

curl <span class="nt">-A</span> <span class="se">\</span>
  <span class="s2">"Mozilla/5.0 (Macintosh; Intel Mac OS X x.y; rv:42.0) Gecko/20100101 Firefox/42.0"</span> <span class="se">\</span>
  http://localhost:8080
</code></pre></div></div>

<h3 id="option-o">-o</h3>

<p><code class="language-plaintext highlighter-rouge">-o</code> 옵션을 사용하면 다운로드한 내용을 파일로 저장할 수 있다.</p>

<p>다음은 curl의 [[/cmd/man]]{man page}에서 소개하는 예제이다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>curl <span class="nt">-o</span> file https://example.com
curl <span class="s2">"http://{one,two}.example.com"</span> <span class="nt">-o</span> <span class="s2">"file_#1.txt"</span>
curl <span class="s2">"http://{site,host}.host[1-5].example"</span> <span class="nt">-o</span> <span class="s2">"#1_#2"</span>
curl <span class="nt">-o</span> file https://example.com <span class="nt">-o</span> file2 https://example.net
</code></pre></div></div>

<h3 id="option-data">--data</h3>

<p><code class="language-plaintext highlighter-rouge">-d</code>, <code class="language-plaintext highlighter-rouge">--data</code> 옵션을 쓰면 body를 지정할 수 있다.</p>

<p>아래와 같이 <code class="language-plaintext highlighter-rouge">-d</code>를 여러번 사용할 수 있다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="c"># form 보내기</span>
curl <span class="se">\</span>
  <span class="nt">-d</span> <span class="nv">name</span><span class="o">=</span><span class="s2">"John Grib"</span> <span class="se">\</span>
  <span class="nt">-d</span> <span class="nv">hobby</span><span class="o">=</span><span class="s2">"coding"</span> <span class="se">\</span>
  https://httpbin.org/anything
</code></pre></div></div>

<p>물론 json을 직접 입력하는 방법이 가장 흔한 방법 중 하나일 것이다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="c"># json 보내기</span>
curl <span class="nt">-d</span> <span class="s1">'{"name":"john"}'</span> <span class="se">\</span>
  <span class="nt">-H</span> <span class="s2">"Content-Type: application/json"</span> <span class="se">\</span>
  https://httpbin.org/anything
</code></pre></div></div>

<p>body에 입력할 내용이 길어서 번거롭다면 파일로 저장해서 파일 이름을 지정하는 것이 편하다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="c"># json 파일 이름을 지정하여 파일 내용 보내기</span>
curl <span class="nt">-d</span> @filename <span class="se">\</span>
  <span class="nt">-H</span> <span class="s2">"Content-Type: application/json"</span> <span class="se">\</span>
  https://httpbin.org/anything
</code></pre></div></div>

<ul>
  <li><code class="language-plaintext highlighter-rouge">-d @FILE_NAME</code>: 텍스트 파일의 내용을 보낼 수 있다.
    <ul>
      <li><code class="language-plaintext highlighter-rouge">{"name":"john"}</code>을 내용으로 갖는 파일이 있다면 위와 같이 사용할 수 있다.</li>
    </ul>
  </li>
</ul>

<p>아래와 같이 <code class="language-plaintext highlighter-rouge">@-</code>를 사용하면 stdin을 body로 지정할 수 있다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="c"># stdin 사용하기</span>
<span class="nb">echo</span> <span class="s1">'{"name":"johngrib"}'</span> <span class="se">\</span>
  | curl <span class="nt">-H</span> <span class="s2">"Content-Type: application/json"</span> https://httpbin.org/anything <span class="nt">-d</span> @-
</code></pre></div></div>

<h3 id="option-json">--json</h3>

<p><a href="https://daniel.haxx.se/blog/2022/02/02/curl-dash-dash-json/">CURL DASH-DASH-JSON</a></p>

<p>curl에 <code class="language-plaintext highlighter-rouge">--json</code> 옵션이 추가되었다.</p>

<p>아래의 두 명령은 똑같이 작동한다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>curl <span class="nt">-d</span> <span class="s1">'{"name":"john"}'</span> <span class="se">\</span>
  <span class="nt">-H</span> <span class="s2">"Content-Type: application/json"</span> <span class="se">\</span>
  https://httpbin.org/anything
</code></pre></div></div>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>curl <span class="nt">--json</span> <span class="s1">'{"name":"john"}'</span> <span class="se">\</span>
  <span class="nt">-H</span> <span class="s2">"Content-Type: application/json"</span> <span class="se">\</span>
  https://httpbin.org/anything
</code></pre></div></div>

<p>물론 아래와 같이 해도 된다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>curl <span class="nt">--json</span> @filename <span class="se">\</span>
  <span class="nt">-H</span> <span class="s2">"Content-Type: application/json"</span> <span class="se">\</span>
  https://httpbin.org/anything
</code></pre></div></div>

<p>아래와 같이 <code class="language-plaintext highlighter-rouge">@-</code>도 쓸 수 있다.</p>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nb">echo</span> <span class="s1">'{"name":"john"}'</span> | curl <span class="nt">--json</span> @- <span class="se">\</span>
  <span class="nt">-H</span> <span class="s2">"Content-Type: application/json"</span> <span class="se">\</span>
  https://httpbin.org/anything
</code></pre></div></div>

<h3 id="-i--response-header-출력">-i : response header 출력</h3>

<p><code class="language-plaintext highlighter-rouge">-i</code> 옵션을 사용하면 response header를 출력한다.</p>

<h3 id="http-버전-지정">http 버전 지정</h3>

<div class="language-sh highlighter-rouge"><div class="highlight"><pre class="highlight"><code>curl <span class="nt">--http1</span>.0 http://httpbin.org/ip
</code></pre></div></div>

<p>지정 가능한 옵션은 다음과 같다.</p>

<ul>
  <li><code class="language-plaintext highlighter-rouge">--http0.9</code></li>
  <li><code class="language-plaintext highlighter-rouge">--http1.0</code> 또는 <code class="language-plaintext highlighter-rouge">-0</code></li>
  <li><code class="language-plaintext highlighter-rouge">--http1.1</code></li>
  <li><code class="language-plaintext highlighter-rouge">--http2-prior-knowledge</code></li>
  <li><code class="language-plaintext highlighter-rouge">--http2</code></li>
  <li><code class="language-plaintext highlighter-rouge">--http3-only</code></li>
  <li><code class="language-plaintext highlighter-rouge">--http3</code></li>
</ul>

<h3 id="그-외-기타-옵션들">그 외 기타 옵션들</h3>

<ul>
  <li><code class="language-plaintext highlighter-rouge">--data-urlencode</code>: URL encode를 사용한다.</li>
  <li><code class="language-plaintext highlighter-rouge">--data-binary</code>: 바이너리 데이터를 전송할 때 사용한다.</li>
</ul>

<h2 id="참고문헌">참고문헌</h2>

<ul>
  <li><a href="https://curl.se/docs/history.html">curl / Docs / Project / History of curl</a></li>
  <li><a href="https://daniel.haxx.se/blog/2021/03/20/curl-is-23-years-old-today/">curl 23주년을 기념하는 Daniel Stenberg의 포스트 (2021-03-20)</a></li>
  <li><a href="https://daniel.haxx.se/blog/2022/02/02/curl-dash-dash-json/">–json 옵션의 추가를 알리는 Daniel Stenberg의 글 (2022-02-02)</a></li>
</ul>


    </article>



    <div class="giscus"></div>
    <div class="post-comments">
    <script src="https://giscus.app/client.js"
        data-repo="johngrib/johngrib.github.io"
        data-repo-id="MDEwOlJlcG9zaXRvcnkxMTIwODgwNDc="
        data-category="Announcements"
        data-category-id="DIC_kwDOBq5T784CTYB0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="light"
        data-lang="ko"
        crossorigin="anonymous"
        data-mapping="specific"
        data-term="B6/81C877-1018-4D8C-832C-D96244F87479"
        data-strict="0"
        async>
    </script>
    </div>



</div>
<script async src="/js/create-link.js"></script>


<script async>
    ;(function() {
        const tableList = document.querySelectorAll('.table-generate');

        if (tableList == null) {
            return;
        }

        for (let i = 0; i < tableList.length; i++) {
            const ul = tableList[i];
            const draw = {
                th: '',
                td: ''
            };

            const rows = ul.children;
            for (let j = 0; j < rows.length; j++) {
                const row = rows[j].children[0];
                const columns = row.children;
                const isHeader = /^th/.test(rows[j].innerHTML);
                const colTag = isHeader ? 'th' : 'td';

                let colData = '';
                for (let k = 0; k < columns.length; k++) {
                    const column = columns[k];
                    const content = column.innerHTML;
                    colData += `<${colTag}>${content}</${colTag}>`;
                }

                const trHtml = `<tr>${colData}</tr>`

                draw[colTag] += trHtml;
            }

            const result = `
                <table>
                    <thead>${draw.th}</thead>
                    <tbody>${draw.td}</tbody>
                </table>`

            const targetId = ul.getAttribute('data-target-id');
            document.getElementById(targetId).innerHTML = result;
            ul.remove();
        }
    })();
    ;(function() {
        const source = document.querySelectorAll('.dynamic-insert');

        if (source == null) {
            return;
        }

        for (let i = 0; i < source.length; i++) {
            const item = source[i];

            const target = item.getAttribute('data-target-selector');
            document.querySelector(target).innerHTML = item.outerHTML;
            item.remove();
        }
    })();
</script>


<script async src="/js/parent.js"></script>
<script async src="/js/toc-highlight.js"></script>

            </div>
        </div>
        <footer class="footer">
    <div>

    </div>
</footer>
    </body>
</html>
```
