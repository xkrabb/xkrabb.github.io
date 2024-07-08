---
title: angular笔记2-模板语法
date: 2024-07-08 22:25:18
tags: [angular, 笔记]
---

### 组件类

```javascript
@Component({
  standalone: true,
  templateUrl: "appcomponent.html",
})
export class AppComponent {
  title = "app header";
  heroes = [{ name: "hero1" }, { name: "hero2" }];
  itemImageUrl = "../assets/phone.svg";
  item = "value pass child comp";

  deleteItem() {}
  onSave(ev: Event) {}
  deleteHero(hero: any) {}
  onSubmit(form: any) {}
}
```

<!--more-->

### 模板

```html
<!-- appcomponent.html -->

<!-- 1 文本插值 -->
<h1>{{title}}</h1>

<!-- 2 模板语句，不支持 new,++,--,|,&,管道；参数为语句上下文，不是组件属性 -->

<button type="button" (click)="deleteItem()">Delete hero</button>
<button type="button" (click)="onSave($event)">Save</button>
@for (hero of heroes; track hero) {
<button type="button" (click)="deleteHero(hero)">{{ hero.name }}</button>
}

<form #heroForm (ngSubmit)="onSubmit(heroForm)">...</form>

<!-- 3 property属性绑定  -->

<img alt="item" [src]="itemImageUrl" />

<!-- 子组件通过 @Input item: string 接收 -->

<item-detail [item]="item" />

<!-- 4 attribute属性绑定 -->
<tr>
  <td [attr.colspan]="1 + 1">One-Two</td>
</tr>

<!-- 5 类和样式绑定 -->
<p [class.sale]="apllyWhenTrue">class style</p>
<p [class]="clsWithWhiteSpace">class style</p>
<p [class]="clsArray">class style</p>
<p [class]="clsRecord">class style</p>

<span [style.backgroundColor]="colorValue">style</span>

<div [style.width.px]="widthValue">style</div>
<div [style]="styleWithSemicolon">style</div>
<div [style]="styleRecord">style</div>

<!-- 6 事件绑定 -->

<button (click)="onSave()">Save</button>

<!-- 需要配套ClickDirective指令  -->

<button type="button" (myClick)="clickMessage">custome event</button>

<!-- 键盘事件，多键组合 -->

<input (keydown.code.shiftleft.altleft.keyt)="onKeydown($event)" />

<!-- 被动事件，用于优化，如滚动条 -->

<!-- 7 双向绑定 -->
<!-- 使用属性和事件绑定，子组件内必须@Input和@Output，而且命名有约定 sizeChange -->

<app-sizer [(size)]="fontSizePx"></app-sizer>

<!-- 8 控制流，不建议用指令？ -->
@if (a > b) {
  {{a}} is greater than {{b}}
} @else if (b > a) {
  {{a}} is less than {{b}}
} @else {
  {{a}} is equal to {{b}}
}

@for (item of items; track item.id; let idx = $index, e = $even) {
  Item #{{ idx }}: {{ item.name }}
} @empty {
  <li> There are no items.</li>
}

@switch (condition) {
  @case (caseA) {
    Case A.
  }
  @case (caseB) {
    Case B.
  }
  @default {
    Default case.
  }
}

<!-- 9 管道， 可以自定义，使用需要导入对应Pipe -->
<p>The hero's birthday is in {{ birthday | date:'yyyy' | uppercase }}</p>
<!-- 管道值是纯更新，可以缓存优化，引用监测不到变化； @Pipe中pure: false修改监测为非纯 -->
<!-- 异步管道，优化异步值处理 message$: Observable<string>; -->
<p>{{ message$ | async }}</p>

<!-- 10 模板引用变量 以#开头；组件上引用实例；标签引用元素；ng-tmpealte引用emplateRef -->
<label for="customer-input"
  >Type something: <input id="customer-input" #customerInput />{{
  customerInput.value }}
</label>
<input type="text" #box (keyup.enter)="addHero(box.value); box.value=''" />
<!-- 带名字，则指向指令或组件 -->
<form #itemForm="ngForm" (ngSubmit)="onSubmit(itemForm)">
  <label for="name">Name</label>
  <input
    type="text"
    id="name"
    class="form-control"
    name="name"
    ngModel
    required
  />
  <button type="submit">Submit</button>
</form>

<!-- 11 svg模板，同普通模板一样-->
<svg>
  <g>
    <rect
      x="0"
      y="0"
      width="1"
      height="1"
      [attr.fill]="fillColor"
      (click)="changeColor()"
    />
    <text x="120" y="50">click the rectangle to change the fill color</text>
  </g>
</svg>
```
