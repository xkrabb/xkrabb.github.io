---
title: angular笔记1-组件
date: 2024-07-07 20:05:06
tags: [angular, 笔记]
---

换项目了，技术栈的用 angular。这技术栈国内并没有国外那么流行，估计框架太重，不太符合国内项目轻量快速迭代的要求。开发理念上来说偏向后端的 OOP 编程方式，前端开发人员并不太感冒。整理个学习笔记，也方便查阅。

### 组件

组件的编写形式，及主要配置，声明周期，继承等

<!-- more -->

```javascript
// DemoComp.component.ts

// 装饰器，配置组件元数据
@Component({
  // 独立组件，导入后直接使用； 非独立呢？
  standalone: true,
  // 组件选择器，决定组件的使用方式；全局唯一，大小写敏感
  // 分类：类型，属性 []，css类 .xx
  selector: 'demo-comp',
  // 导入其他组件， 使用 <other-comp>
  imports: [OtherComp],
  // 指定html模板
  templateUrl: 'DemoComp.component.html',
  template: `
    <p>demo</p>
  `,
  // 指定样式
  stylesUrl: 'DemoComp.component.css',
  styles: `
    p {color: "red"}
  `,
  // 决定组件样式作用域，可选值Emulated、 ShadowDom 和 None
  // 默认Emulated，自己独立，受全局影响；ShadowDom完全独立；None认为是全局的
  encapsulation: ViewEncapsulation.None，
  // 组件接收的输入参数，用于继承
  inputs: ['value'],
  // 指定输出(一般是方法)，用于继承
  outputs: ['panelClosed'],
  // 绑定到宿主元素，同在组件上绑定。如果有冲突则有处理规则
  // 都是静态则host优先，动态静态则动态优先，都是动态则宿主组件绑定优先
  host: {
    'role': 'slider',
    '[attr.aria-valuenow]': 'value',
    '[tabIndex]': 'disabled ? -1 : 0',
    '(keydown)': 'updateValue($event)',
  },
  // 组件检查更新策略，默认交互，网络，定时器等
  //  ChangeDetectionStrategy.OnPush，绑定值，监听事件
  changeDetection： ChangeDetectionStrategy.OnPush,
  // 模板空白是否清除
  PreserveWhitespace: true
})
export class DemoComp {
  // 定义动态数据 和 方法，使用配合查看html
  name = 'comp';
  disabled = false;
  testId = '123';
  isAdmin = true;

  // 可接收组件的输入参数; 必填，输入转换
  // trFun需要是纯函数, alias别名（组件调用参数别名）
  // 不建议getter/setter方法
  @Input({required: true, transform: trFun， alias: nName}) value = 0;

  // 装饰器来定义自定义事件，组件使用处方法； 配合EventEmitter
  // 通过 this.panelClosed.emit(params) 触发父组件调用处绑定的事件
  // 将推出ouput函数，更推荐
  @Output('reName') panelClosed = new EventEmitter<void>();

  ingredientList = [
    {name: 'noodles', quantity: 1},
    {name: 'miso broth', quantity: 2},
  ];

  updateName() {
    this.name = ‘yy’
  }

  // 生命周期
  // 初始化
  ngOnInit() {}
  // 第一次change，在Init前
  ngOnChange(changes: SimpleChanges) {}
  // 组件销毁，不再显示
  // DestroyRef 可以注入，传递给其他组件做销毁处理
  ngOnDestory() {}
  // 每次检查组件模板变化之前，第一次执行在Init之后
  onDoCheck() {}
  // 所有子元素初始化后运行
  onAfterViewInit() {}
  // 所有内嵌子元素初始化后运行
  onAfterContentInit() {}
  // 每次所有子元素检查后运行，有性能影响
  onAfterViewChecked() {}
  // 每次所有内嵌子元素检查后运行，有性能影响
  onAfterContentChecked() {}

  constructor(elementRef: ElementRef) {
     // 组件渲染Dom后调用，需要注入; 读写阶段优先
    afterRender(callback, {phase: AfterRenderPhase.Write})
    afterNextRender(callback, {phase: AfterRenderPhase.Read})
    // 获取dom引用，只在afterRender操作dom
    const ele = elementRef.nativeElement
  }
}
```

### 外链模板

```html
<!-- DemoComp.component.html -->

<!-- 可使用样式标签  -->
<style></style>

<!-- 动态内容指定 -->
<p>demo {{name}}</p>
<!-- 动态属性需要通过 [] 指定 -->
<input [disabled]="disabled" />
<!-- 非dom标准动态属性需要 attr 前缀 -->
<p [attr.data-test-id]="testId">attr data</p>
<!-- 绑定事件 () 指定 -->
<button (click)="updateName($event)">changeName</button>
<!-- 条件渲染 -->
@if (isAdmin) {
<button>Erase database</button>
} @else {
<p>You are not authorized.</p>
}
<!-- 循环渲染， 带track指定唯一性值，类似key -->
<ul>
  @for (ingredient of ingredientList; track ingredient.name) {
  <li>{{ ingredient.quantity }} - {{ ingredient.name }}</li>
  }
</ul>
<!-- slot占位，由父组件决定； 多个时候select，ngProjectAs（只能静态）指定 -->
<div>
  <ng-content select="card-title" />
  <ng-content />
  <ng-content ngProjectAs="card-body" />
</div>
```

### 调用的父组件

```javascript
@Component({
  standalone: true,
  selector: 'parent-comp',
  template: `
    <demo-comp (panelClosed)="fun()" nName="name from parent" role=“actor”>
      <p>替换默认位置，不带select的</p>
      <card-title>xxxxxx</card-titile>
      <card-body>yyyyy</card-body>
    </demo-comp>
  `,
})
export class DemoComp {
  // 查询第一个参数是定位器：dom，指令，组件，template，web component
  // 视图查询 利用this.demo可以调用查询到的子组件信息
  @ViewChild(DemoComp，{
    static: true，// 存在且不是条件渲染
    descendants：true，// 默认查一层子元素，可以设置true查询多层，单不会穿透自身模板
    read: TemplateRef， // 读取特定值
  }) demo: DemoComp
  @ViewChildren(DemoComp) demos: QueryList<DemoComp>
  // 内容查询（嵌入内容）
  @ContentChild(DemoComp) demo: DemoComp
  @ContentChildren(DemoComp) demos: QueryList<DemoComp>

  fun(){}
}
```

### 外链样式

```css
/* DemoComp.component.css */
p {
  background: "red";
}
```

### 继承

```javascript
@Component({
  selector: 'base-listbox',// 被覆盖
  template: ` ...`, // 被覆盖
  host: {
  '(keydown)': 'handleKey($event)', // 被继承
  },
})
export class ListboxBase {
  @Input() value: string; // 被继承
  constructor(private element: ElementRef) { }
  handleKey(event: KeyboardEvent) {}
  onInit() {}
}

@Component({
  selector: 'custom-listbox',
  template: `...`,
  host: {
  '(click)': 'focusActiveOption()',
},
})
export class CustomListbox extends ListboxBase {
  @Input() disabled = false;
  constructor(element: ElementRef) {
    super(element); // 继承父类注入
  }
  focusActiveOption() {}
  override onInit() { // 重写声明周期方法
  super.onInit()
  }
}
```

### 懒加载

```javascript
@Component({
  template: ` <section>
      <h2>Basic settings</h2>
      <basic-settings />
    </section>
    <section>
      <h2>Advanced settings</h2>
      <button (click)="loadAdvanced()" *ngIf="!advancedSettings">
        Load advanced settings
      </button>
      <ng-container *ngComponentOutlet="advancedSettings" />
    </section>`,
})
export class AdminSettings {
  advancedSettings: { new(): AdminSettings } | undefined;
  async loadAdvanced() {
    this.advancedSettings = await import("path/to/advanced_settings.js");
  }
}
```
