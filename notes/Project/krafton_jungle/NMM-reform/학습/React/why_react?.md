# why react?




## 생태계가 넓음

- 커뮤니티가 크고 자료가 많다
- 좋은 플러그인은 다 리액트 지원
- 꼭 그런건 아니긴 함


## (Passive) Single Page Application; UX good

html, css, js를 매번 다운받지 않고, api로 json데이터 따위만 받고 리렌더링 하니까 성능이
좋다. 깜빡임도 없고.

대신 초기에 가져와야하는 덩어리가 커서 첫 화면 띄우는게 다소 느림
SEO도 불리


## (Passive) Virtual DOM 사용으로 좋은 성능

- **실제 DOM 조작은 느림**  
  (브라우저가 화면을 다시 그려야 하므로)
- React는 **가상 DOM에서 먼저 계산**하고,  
  바뀐 부분만 실제 DOM에 반영해서 **성능을 높임**

    - **가상 DOM**은 실제 브라우저의 DOM(Document Object Model)을 **자바스크립트 객체로 메모리상에 복제**해 놓은 것
    - React는 UI 상태가 바뀔 때마다 **가상 DOM 트리를 새로 만듦**
    - 이전 가상 DOM과 새 가상 DOM을 비교해서, 실제 DOM에 꼭 필요한 변경만 적용함


- 덕분에 **전체 UI를 다시 그리는 것처럼 코딩**해도, 실제로는 **필요한 부분만 빠르게 바뀜**


## 컴포넌트 (부모 -> 자식)

- 조립식 컴포넌트; 재사용성
- 전체 DOM 구조를 신경쓰지 않고 개별 컴포넌트 내부만 신경쓰면 된다.
    - 태그의 부모/자식 관계, className, id 따위 신경 안써도 된다.
- script(JS)와 html의 통합; JS변수를 통해 가변적인 html을 선언적으로 작성할 수 있다.(aka. 다루기 쉽다.)
- Virtual DOM 사용으로 성능이 좋다.


```jsx
import

MyComponent = {
    state = something;
    action () {
        state += 1
    }

    return {
        <button onClick = action()>

        if (state == a) 
            <html_A>
            <otherComponent>
        else 
            <html_B>
    }
}
```




## 단방향 데이터 흐름; 상태 관리와 디버깅이 쉬움

- 보통 **부모 컴포넌트 → 자식 컴포넌트**로 데이터가 전달
- 자식 컴포넌트는 받은 상태(데이터)를 **읽기만 가능**, 수정은 불가능
- 꼭 필요하다면 setter 함수를 전달

- 보완적으로 전역 상태 관리 개념이 존재함

