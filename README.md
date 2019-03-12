##number   vue数字键盘(自定义)

npm i vue-dsigital-keyboard -S
根据项目安装

## Build Setup
``` bash

import msg from 'vue-dsigital-keyboard';
#引入vue中
在components注册

components:{
        Msg
    },
在模板中使用
<Msg></Msg>

#父组件通过绑定confirmEvent 来取到键盘输出的数据


