# 单测自动mock生成工具"automock"使用说明
这两天一直在写单测，写的我脑壳疼，一个方法里面调用了很多的别的方法，基本上每个调用的方法都需要mock，这样能保证单测的独立性。每次想要mock的时候，总要去看一下调用方法的返回值和返回类型，然后自己手动复制粘贴，写Return(），有时候一不小心就忘记写Build（）从而导致panic。突发奇想花了一天时间写了这个，写函数mock时候的效率可以提升好几倍，妈妈再也不用担心我mock方法写的慢啦！

注：结合单元测试最佳实践 里的自动生成test工具 与 单测在管理方向的实践与案例分享 里的嵌套与同一函数多次 mock 使用更佳 
## 主要功能
通过复制（crtl c) 想要mock的函数定义部分，自动生成该函数的mock代码
例如：
复制：func (p *ProcessConfigServiceImpl) getHTTP() *http.Client {
会自动生成： Mock((*ProcessConfigServiceImpl).getHTTP).Return(&http.Client{}).Build() 并写入剪切板中，只需要（crtl v)即可写入单测文件中


## 获取方法
go get github.com/chaoleezzz/automock@lastest
运行命令为 automock，运行中程序会监听剪切板，功能生效


## 主要原理
主要基于字符串特征匹配与剪切板的监听来实现
剪切板每0.5s监听一次，降低cpu使用不影响使用体验

有下述特征的字符串不会被工具处理（保持复制后的状态）
1. 不是以func开头和 { 结尾的字符串
2. 没有一个返回值的函数（方法）
3. 字符串中小括号对数大于3对且括号不匹配的文本（一般的方法函数最多三对匹配的小括号）

## 使用注意事项
由于本人水平有限，尽量遵照以下规定来使用，防止程序发生意外崩溃需要重新启动
1. 对于方法函数和普通函数都适用，并且支持方法头有换行的情况（当然整个复制在一行最好，防止意外错误）
2. 源代码方法尽量golint过，防止发生意外的错误
3. 如果有参数是error 类型，会被自动mock返回为nil
4. 结构体类型返回mock后会被自动默认初始化
5. 指针类型返回mock后会被自动默认初始化并加上取地址符号&
6. 列表类型返回mock后会被自动默认初始化并在列表中有一个默认实例
7. 基本类型都会返回其基本类型的默认值（特殊：字符串会默认返回"thisIsString")
8. 基于剪切板，无法自动识别没有显式调用其他包的包名，有需要的情况下需要手动添加（在调用者或者返回的类型前加上包名.  即可
9. 基于剪切板，无法自动识别返回类型为接口的情况，需要手动将接口类型名改为实现类型名：比如 IXXXX 改为 XXXXManager 即可

## 说在最后
这个小工具代码量不多，整体上还是一个开发的阶段，所以有很多不完善，遇到什么问题或者有什么建议都欢迎在本文档中评论或者一起讨论，后续如果有需要的话也可以 加上 UI 、或者可以配置文件中配置参数、或者在GOLAND中插件化（这样可以获取到文本里面获取不到的信息，比如是不是接口等）……哈哈，至少这东西能让我单测少打很多字，提高了我的单测生产力，大家如果也觉得有用那太好了，欢迎补充！
