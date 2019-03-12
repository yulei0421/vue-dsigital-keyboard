<!--********************************************************************
 * 父组件通过绑定confirmEvent 来取到键盘输出的数据
********************************************************************-->
<template>
    <div class='key-container'>
        <div class='key-title'>请输入金额</div>
        <div class='input-box'>{{ money }}</div>
        <div class='keyboard' @click.stop='_handleKeyPress'>
            <div class='key-row' >
                <!-- {{item}} -->
                <div class='key-cell'    v-for="item in data1.slice(0,4)">{{item}}</div>
            </div>
             <div class='key-row' >
                <!-- {{item}} -->
                <div class='key-cell'    v-for="item in data1.slice(4,8)">{{item}}</div>
            </div>
            <div class='key-row' >
                <!-- {{item}} -->
                <div class='key-cell'    v-for="item in data1.slice(8,12)">{{item}}</div>
            </div>
            <div class='key-row' >
                 <div class='key-cell'>清空</div>
                  <div class='key-cell'  v-for="item in data1.slice(12,13)">{{item}}</div>
                  <div class='key-cell'>确认</div>
            </div>
            <!-- <div class='key-confirm' data-num='S'>确认</div> -->
        </div>
    </div>
</template>

<script>
    export default{
    	data(){
    		return{
                money : '',
                 data1:[1,3,5,7,9,2,4,6,8,'.','c',0,null,]
            }
        },
        mounted(){
           this.$set(
               this.data1,this.shuffle(this.data1)//数据只是顺序不一样vue的监听机制无法进行查询所以用set来进行操作
           )
        },
        methods : {
            //打乱数据排序
            shuffle(arr) {
                var len = arr.length;
                    for (var i = 0; i < len - 1; i++) {
                        var index = parseInt(Math.random() * (len-i));
                        var temp = arr[index];
                        arr[index] = arr[len - i - 1];
                        arr[len - i - 1] = temp;
                    }
                    // console.log(arr)
                return arr;
            },
             //处理按键
			_handleKeyPress(e) {
                // console.log(e)
                let num = e.target.innerHTML;
                console.log(num)
				//不同按键处理逻辑
				// -1 代表无效按键，直接返回
				if (num == '') return false;
				switch (String(num)) {
					//小数点
					case '.':
						this._handleDecimalPoint();
						break;
					//删除键
					case 'c':
						this._handleDeleteKey();
						break;
					//清空键
					case '清空':
						this._handleClearKey();
						break;
					//确认键
					case '确认':
						this._handleConfirmKey();
						break;
					default:
						this._handleNumberKey(num);
						break;
				}
			},
			//处理小数点函数
			_handleDecimalPoint() {
				//如果包含小数点，直接返回
				if (this.money.indexOf('.') > -1) return false;
				//如果小数点是第一位，补0
				if (!this.money.length)
					this.money = '0.';
				//如果不是，添加一个小数点
				else
					this.money = this.money + '.';
			},
			//处理删除键
			_handleDeleteKey() {
				let S = this.money;
				//如果没有输入，直接返回
				if (!S.length) return false;
				//否则删除最后一个
				this.money = S.substring(0, S.length - 1);
			},
			//处理清空键
			_handleClearKey() {
				this.money = '';
			},
			//处理数字
			_handleNumberKey(num) {
				let S = this.money;
				//如果有小数点且小数点位数不小于2
				if ( S.indexOf('.') > -1 && S.substring(S.indexOf('.') + 1).length < 10)
					this.money = S + num;
				//没有小数点
				if (!(S.indexOf('.') > -1)) {
					//如果第一位是0，只能输入小数点
					if (num == 0 && S.length == 0)
						this.money = '0.';
					else {
						if (S.length && Number(S.charAt(0)) === 0) return;
						this.money = S + num;
					}
				}
			},
			//提交
			_handleConfirmKey() {
                let S = this.money;
                console.log(S)
				//未输入
				if (!S.length){
					alert( '您目前未输入!' )
					return false;
				}
				//将 8. 这种转换成 8.00
				if (S.indexOf('.') > -1 && S.indexOf('.') == (S.length - 1))
					S = Number(S.substring(0, S.length - 1)).toFixed(2);
				//保留两位
				S = Number(S).toFixed(2);
				this.$emit('confirmEvent',S)
			}
        }
    }
</script>

<style scoped>
    .key-container {
        margin-top: 50px;
        width: 100%;
        display: flex;
        display : -webkit-flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
    }
    .input-box {
        font-size: 35px;
        font-weight: bold;
        height: 40px;
        border-bottom: 1px solid #aaa;
        padding: 10px 15px;
        text-align: right;
        width: 90%;
    }
    .keyboard {
        position: fixed;
        bottom: 0;
        left: 0;
        height: 240px;
        width: 100%;
    }
    .keyboard .key-row {
        display: flex;
        display: -webkit-flex;
        position: relative;
        height: 60px;
        line-height: 60px;
    }
    .keyboard .key-row::before {
        content: '';
        position: absolute;
        left: 0;
        top: 0;
        right: 0;
        height: 1px;
        border-top: 1px solid #d5d5d6;
        color: #d5d5d6;
        -webkit-transform-origin: 0 0;
        transform-origin: 0 0;
        -webkit-transform: scaleY(0.5);
        transform: scaleY(0.5);
    }
    .keyboard .key-cell {
        flex: 1;
        -webkit-box-flex: 1;
        text-align: center;
        position: relative;
    }
    .keyboard .key-cell::after {
        content: '';
        position: absolute;
        overflow: hidden;
        top: 0;
        right: 0;
        bottom: 0;
        height: 200%;
        border-right: 1px solid #d5d5d6;
        color: #d5d5d6;
        -webkit-transform-origin: 0 0;
        transform-origin: 0 0;
        -webkit-transform: scaleY(0.5);
        transform: scaleY(0.5);
    }
    .keyboard .key-cell:nth-last-child(1)::after {
        border-right: 0;
    }
    .keyboard .disabled {
        background: rgba(0, 0, 0, 0.2);
    }
    .keyboard .key-confirm {
        position: absolute;
        text-align: center;
        height: 118px;
        width: 25%;
        line-height: 118px;
        background: #fff;
        z-index: 5;
        right: 0;
        bottom: 1px;
    }
</style>