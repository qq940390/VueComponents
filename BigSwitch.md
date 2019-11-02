# BigSwitch

移动端适用的仿 iPhone 来电滑动组件

## 预览

![BigSwitch_example.gif](https://i.loli.net/2019/11/02/4HFuzJRnUoGiNY7.gif)

## 使用方法

1. 复制 BigSwitch 文件夹到你的项目存放组件的位置

2. vue 代码部分
   tip：要显示的
   loopTime：闪亮效果循环一次的毫秒时间；
   speed：每个字闪亮的快慢，越小越快；
   effectTime：闪亮效果的持续时间，越大越长；
   onDragPass：当拖动滑到最右时的回调函数。

    ```html
    <big-switch tip="滑动来进入" loopTime="2500" speed="100" effectTime="800" :onDragPass="dragPass"></big-switch>
    ```

3. JS 代码部分

    ```javascript
    import BigSwitch from 'BigSwitch'
    export default {
        components: {
            BigSwitch
        },
        methods: {
            dragPass() {
                console.log('dragPass')
            }
        }
    }
    ```
