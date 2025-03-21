# 󰏢 general tips




## '웹 접근성 기반 선택자'를 활용하자

예를 들어, 아래처럼 html 태그를 선택할 수 있다.

```html
<button aria-label="Download">Download</button>
```

```js
document.querySelector('button[aria-label="Download"]');
```

이 선택자는 aria-label 속성에 "Download"가 설정된 모든 버튼을 선택한다. 버튼의 클래스나 ID가
변경되더라도 이 선택자는 유지될 가능성이 높다.
