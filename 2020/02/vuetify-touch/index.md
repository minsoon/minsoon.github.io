# [BugReport] Vuetify v-ripple 중복 이슈


vue 프로젝트를 진행하면서 언젠가 부터인지는 정확하지 않으나 touch 효과가 중복되어 터지는 에러를 발견하였다.

이에따라 vuetify 이슈 사항에 등록되었어 업데이트가 어려운 사항에 따라 수정한 내역이다.

나중에 vuetify를 다시 업데이트 할때 해결은 되겠지만 그전에 해결하기 위해서 수정한 내역이다.


``` javascript
// node_modules/vuetify/src/globals.d.ts

interface HTMLElement {
    _clickOutside?: EventListenerOrEventListenerObject
    _onResize?: {
      callback: () => void
      options?: boolean | AddEventListenerOptions
    }
    _ripple?: {
      enabled?: boolean
      centered?: boolean
      class?: string
      circle?: boolean
      touched?: boolean
      isTouch?: boolean  // Add
    }
    _onScroll?: {
      callback: EventListenerOrEventListenerObject
      options: boolean | AddEventListenerOptions
      target: EventTarget
    }
    _touchHandlers?: {
      [_uid: number]: TouchStoredHandlers
    }
}
```

``` javascript
// node_modules/vuetify/es5/directives/ripple.js
// node_modules/vuetify/lib/directives/ripple.js
// node_modules/vuetify/src/directives/ripple.ts


function rippleShow(e) {
    var value = {};
    var element = e.currentTarget;
    if (!element || !element._ripple || element._ripple.touched) return;
    if (isTouchEvent(e)) {
        element._ripple.touched = true;
        element._ripple.isTouch = true; // Add
    } else {
      if (element._ripple.isTouch) return // Add
    }
    value.center = element._ripple.centered;
    if (element._ripple.class) {
        value.class = element._ripple.class;
    }
    ripple.show(e, element, value);
}
```

나같은 경우는 Ripple를 es5에서 땡겨오기 때문에 `node_modules/vuetify/es5/directives/ripple.js` 파일만 수정했다.

vuetify가 다시 설치되면 또 똑같이 node_modules을 수정해야하는 번거로움이 있어 최선의 방법은 아니라고 생각한다..

버전 올리는걸로 다시한번 생각해 봐야겠다..


### 참고 자료 ###
- [[Bug Report] Ripple effect happens twice in mobile browsers #7605](https://github.com/vuetifyjs/vuetify/issues/7605)
- [fix(Ripple): add ripple property to denote touch enabled](https://github.com/vuetifyjs/vuetify/commit/c8d2a675ea9c02f1c947b435f6aca0978c80e4b9)

