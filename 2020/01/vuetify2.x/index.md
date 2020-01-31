# Update Vuetify 1.5 -> 2.x



Vue 프로젝트를 진행하면서 vuetify 1.x에 대한 버그(v-ripple: 모바일에서 중복으로 터짐)가 

발견되면서 패키지 자체를 수정해서 쓰다가 이제서야 오려봅니다.

나는 Less를 사용중인데 이번에 vuetify 2.x는 sass를 사용하더라...

충돌이 날까 걱정했지만 다행히 그런 문제는 없었다.

일단 vuetify 버전을 올리고 sass 및 loader를 설치해준다.
``` npm
$ npm install vuetify@2.2.8
$ npm install sass sass-loader fibers deepmerge -D
```

그리고 webpack.conf에서 loader를 설정해 주는데 sass-loader 버전에 따라 다르다.

![Local Picture](/images/error.png "Local Picture")

안그러면 이렇게.....

``` javascript
// webpack.config.js

module.exports = {
  rules: [
    {
      test: /\.s(c|a)ss$/,
      use: [
        'vue-style-loader',
        'css-loader',
        {
          loader: 'sass-loader',
          // Requires sass-loader@^7.0.0
          options: {
            implementation: require('sass'),
            fiber: require('fibers'),
            indentedSyntax: true // optional
          },
          // Requires sass-loader@^8.0.0
          options: {
            implementation: require('sass'),
            sassOptions: {
              fiber: require('fibers'),
              indentedSyntax: true // optional
            },
          },
        },
      ],
    },
  ],
}
```

다음 1.x대 와 다르게 vuetify 플러그인을 따로 빼주게 되어있다.

아래처럼 코드를 변경해주면된다.

``` javascript
// src/plugins/vuetify.js

import Vue from 'vue'
import Vuetify from 'vuetify'
import 'vuetify/dist/vuetify.min.css'

Vue.use(Vuetify)

const opts = {}

export default new Vuetify(opts)
```

``` javascript
// src/main.js

new Vue({
  el: '#app',
  vuetify,
  router,
  components: { App },
  template: '<App/>'
});
```

이제 빌드해서 확인해보니 정상 동작을 한다.

근데 화면이 너무 깨지는 바람에 다시 버전을 내려놨다..어쩔수없지..





