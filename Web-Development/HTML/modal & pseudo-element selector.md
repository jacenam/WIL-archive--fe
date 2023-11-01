# 모달과 가상 요소 셀렉터 (정리 중)

### 목차

- 

***

<br>

일반적으로 모달 팝업을 만드는 형태는 배경을 딤처리할 모달 레이어(`modal-overlay`, `modal-wrap` 등의 클래스 이름을 지정)에 모달 팝업 영역의 콘텐츠가 들어갈 레이어를 추가한다

```html
<div class="modal-wrap">
  <div class=modal-content>
    여기에 모달의 콘텐츠 정보가 입력된다
  </div>
</div>
```

이때 CSS는 아래와 같은 형태다

```css
.modal-wrap {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #10141a66;
}
```

근데 `modal-wrap`처럼 페이지 전체를 뒤덮는 형태의 모달이라면 상관 없겠지만, 만약 내가 진행했던 아마존 클론 프로젝트의 경우 `header`(네비게이션 바)를 제외한 `main` 지역만 모달이 필요할 경우에도 그렇고(그러니까 모달 콘텐츠들이 특정 요소들 사이에 들어가는 경우). 여기에 배경을 딤처리할 `modal-wrap`을 넣을 수도 없을 뿐더러, 넣더라도 코드적으로 이상할 때. 

```html
<div id="nav-wrap">
  <nav class="nav-bar__main">
    <div class="logo">
      <img src="./src/assets/logo.svg" />
    </div>
    <div class="shipping-address">
      <div class="shipping-address__label">
        <img src="./src/assets/location.svg" />
        <p>배송처</p>
      </div>
      <div class="shipping-address__country">
        <p>대한민국</p>
      </div>
      <div class="shipping-address-modal">
        <div class="modal-content__msg">
          <p>KR로 배송할 품목을 표시하겠습니다. 다른 국가로 배송되는 품목을 보려면 배송 주소를 변경하십시오.</p>
        </div>
        <div class="modal-content__btn">
          <button class="continue-btn">계속</button>
          <button class="continue-btn">주소 변경</button>
        </div>
      </div>
    </div>
  </nav>
<div id="nav-wrap">     
```

`modal-wrap`이라는 HTML 코드가 굳이 들어갈 필요가 없다고 생각이 들면. 

가상 요소 셀렉터를 이용할 수 있다. 그러면 굳이 HTML 코드에 `modal-wrap`이라는 HTML 코드를 보여주지 않아도 된다

**Before**

**Before**

```html
<main>
  <div class="modal-wrap">
    <div class="modal-content">
      여기에 모달의 콘텐츠 정보가 입력된다
    </div>
  </div>
</main>
```

```css
.modal-wrap {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #10141a66;
}
```

**After**

```html
<main>
  <div class="modal-content">
    여기에 모달의 콘텐츠 정보가 입력된다
  </div>
</main>
```

```css
/* 가상 요소 셀렉터 활용 */
.main::after {
  content: "";
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #10141a66;
}
```

