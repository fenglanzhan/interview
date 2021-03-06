Angular理解与发现-简书圈友的理解

1. 变化监测机制

   Vue的数据绑定是通过Object.defineProperty接口来实现的，这个地方总是跟Angular的变化监测机制搞混，备注一下。Angular不是用ES5的getter和setter来实现变化监测的，而是以适当的时机去检查对应的值是否被改动。所谓适当的实际就是异步事件发生的时候。通常是在用户操作、setTimeout、ajax回到等异步事件触发之后，Angular捕获这些异步事件的工作是通过Zonejs库来实现的。

   每个组件都有维护一个独立的变化监测器，记录着所属组件的数据变更状态。当Zones捕获到异步事件后，通知Angular执行变化监测操作，每次变化监测操作都始于根组件，并以深度优先的原则向叶组件遍历执行。

1. 给Angular的属性各个赋值方式的区别

   假设父组件A的子组件B的attrB属性

   - 输入属性语法 `<B [attrB]="value"></B>`
     - `attrB`代表`组件B`的输入属性，必须在组件类中定义输入属性`@Input() attrB`，如果没有定义，会报错
     - 会把`value`当做表达式进行计算，即使没有引号包裹
     - 只写`[attrB]`，不进行赋值，不会对`attrB`属性进行赋值操作
   - 标签属性语法`<b attrB="value"></B>`
     - `attrB`可以作为标签属性，又是一个指令，同时勇士一个输入属性时，会分别对标签属性`attrB`和输入属性`attrB`进行赋值，并且使指令`attrB`生效
     - 会把等号右边的`value`当做字符串赋值到`attrB`上，即使value不被引号包裹，也会当做纯字符串
     - 把赋值操作去掉，只写`attrB`时，会对`attrB`进行赋值，为空字符串

2. 给Angular的输出属性赋值的实际意义
   假设父组件A的子组件B的输出属性attrB

   `(attrB)="expression"`，`expression`都会被当做表达式，并且这个表达式是放在Angular自动创建的回调函数的上下文中执行的，拥有局部变量`$event`，是调用`EventEmitter`实例方法`emit`时传入的参数

3. `@ViewChild()`装饰器

   1. 当向`@ViewChild()`装饰器传入一个类型Type时，被装饰的变量引用的是类型为Type的一个子组件
   2. 当向`@ViewChild()`装饰器传入一个字符串时，被装饰的变量引用的是字符串所对应的模板局部变量

4. 组件内容嵌入
   对于符合嵌入的内容则会被嵌入，不符合的内容会从DOM树种移除

5. Angular三种指令的侧重点

   指令的内容是增强模板特性，简介扩展模板语法

   1. 属性指令：扩展元素的属性，常用来改变外观和行为
   2. 结构指令：扩展元素属性，用来改变DOM树结构
   3. 组件：扩展元素标签，自定义标签

   <u>备注：虽然指令常用来扩展属性，组件常用来扩展标签，但是它们的选择器的定义是任意的，所以，组件的选择器也可以定义成属性选择器，从而以属性的形式使用组件，指令的选择器也可以定义成标签选择器，从而以标签的形式使用指令</u>

6. <u>性能优化方式</u>

   1. <u>缩小变化监测范围</u>
      <u>通过变化监测类`ChangeDetectorRef`的实例的`markForCheck():void`方法标记变化监测的路径为根组件到该组件</u>
   2. <u>缩小变化监测时间；</u>
      通过变化监测类ChangeDetectorRef的实例的detach(): void 分离变化监测器，然后在适当的时机通过 detectChanges(): void 手动监测变化 或者 通过reattach(): void 再重新安装上变化监测器；
   3. <u>变化监测策略能用OnPush的不用Default；</u>
   4. <u>模板中的表达式的计算量能少则少；因为Angular的变化监测机制，使得模板中的表达式执行频率很高；</u>
   5. <u>尽量使用NgForTrackBy指令来替代NgFor指令；</u>

7. 服务

   1. providers配置选项是依赖注入操作的关键，它会为该组件创建一个注入器对象，并新建相应服务的实例存储到这个注入器里，当需要引入相应服务的实例时，通过TypeScript的类型匹配即可从注入器取出相应的服务实例对象；
   2. 服务的每一 次（也就是一使用providers声明），该服务都会被建出新的实例，组件的所有子组件均默认继承父组件的注入器对象，复用该注入器里存储的服务实例。这种机制可以保证服务以单例模式运行，除非某个子组件再次注入（即通过providers选项声明）；
   3. 因为所有模块（这里指Angular应用级别的模块，并非ES6语言中的模块）都共享着同一个应用级别的根注入器，所以，当把服务注入到模块里时，该服务在整个应用里均能使用

8. 路由
   路由的作用是建立URL路径和组件之间的对应关系，根据不同的URL路径匹配出相应的组件渲染

9. Angular应用的引导启动
   Angular通过引导运行根模块来启动应用，引导方式有

   1. 动态引导
   2. 静态引导

   Angular应用运行之前，都需要经过编译器对模块、组件等进行编译，编译完成后才开始启动并渲染界面。
   动态引导和静态引导区别在于编译的实际不同，动态引导时将所有的代码加载浏览器后，在浏览器进行编译；而静态引导是将编译过程迁移到开发时的工程打包阶段。
   由于静态引导时，整个应用已经被预先编译，所以编译器并不会被打包的项目代码，使代码包体积更小，加载更快，而且也省去了浏览器编译这个步骤，因此应用的启动也会更快。

   动态引导开发流程简单明了，适合是在小型项目和大型项目的开发阶段使用，而静态引导需要在开发阶段加入预编译流程，稍显复杂但性能提升明显，推荐使用

10. 模板

    1. 模板中的script标签会被忽略
    2. html、body、base等标签在模板中无用

11. 模板表达式
    被禁止的模板表达式：赋值 (=, +=, -=, ...)
    new运算符
    使用;或,的链式表达式
    自增或自减操作符 (++和--)
    和 JavaScript语 法的其它显著不同包括：

    不支持位运算|和&
    具有新的模板表达式运算符，比如|、?.和!。

12. 动画Angular动画是基于标准的Web动画API(Web Animations API)构建的，它们在支持此API的浏览器中会用原生方式工作。
    至于其它浏览器，就需要一个填充库(polyfill)了。你可以从这里获取web-animations.min.js，并把它加入你的页面中。

13. 模块NgModule是一个装饰器函数，它接收一个用来描述模块属性的元数据对象。其中最重要的属性是：

    - declarations - 声明本模块中拥有的视图类。Angular 有三种视图类：组件、指令和管道。
    - exports - declarations 的子集，可用于其它模块的组件模板。
    - imports - 本模块声明的组件模板需要的类所在的其它模块。
    - providers - 服务的创建者，并加入到全局服务列表中，可用于应用任何部分。
    - bootstrap - 指定应用的主视图（称为根组件），它是所有其它视图的宿主。只有根模块才能设置bootstrap属性。

    每个 Angular 应用至少有一个模块（根模块）

    根模块不需要导出任何东西，因为其它组件不需要导入根模块。

    NgModules 和 JavaScript 模块的比较： 

    1. NgModules是应用级别的模块，是以功能特性为划分依据；
    2. JavaScript中的模块是语言级别的模块，是以物理文件或者文件夹为划分依据；

14. 组件
    @Component装饰器实际上就是一个@Directive装饰器，只是扩展了一些面向模板的特性

15. 依赖注入“依赖注入”是提供类的新实例的一种方式，还负责处理好类所需的全部依赖。大多数依赖都是服务。 Angular 使用依赖注入来提供新组件以及组件所需的服务。
    Angular 通过查看构造函数的参数类型得知组件需要哪些服务。

```
当 Angular 创建组件时，会首先为组件所需的服务请求一个注入器 (injector)。

注入器是一个维护服务实例的容器，存放着以前创建的服务实例。 如果所请求的服务实例不在容器中，注入器就会新创建一个服务实例，并且添加到容器中，然后把这个服务返回给 Angular。 当所有请求的服务都被解析完并返回时，Angular 会把这些服务作为参数去调用组件的构造函数。 这就是依赖注入 。

如果注入器还没有HeroService，它怎么知道该如何创建一个呢？
 简单点说，我们必须先用注入器（injector）为服务注册一个提供商（provider）。 提供商用来创建或返回服务实例，通常就是这个服务类本身的实例（相当于new 服务()）。

需要记住的关于依赖注入的要点是：

1. 依赖注入渗透在整个 Angular 框架中，被到处使用。
2. 注入器 (injector) 是本机制的核心。 
   - 注入器负责维护一个容器，用于存放它创建过的服务实例。
   - 注入器能使用提供商创建一个新的服务实例。
3. 提供商是一个用于创建服务的配方。
4. 把提供商注册到注入器。
```

1. 编程思想

   组件类应保持精简。组件本身不从服务器获得数据、不进行验证输入，也不直接往控制台写日志。 它们把这些任务委托给服务。

   组件的任务就是提供用户体验，仅此而已。它介于视图（由模板渲染）和应用逻辑（通常包括模型的某些概念）之间。 设计良好的组件为数据绑定提供属性和方法，把其它琐事都委托给服务。

