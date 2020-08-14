---
layout: post
title:  "CoordinatorLayout与Behavior"
description: CoordinatorLayout与Behavior协作产生炫酷效果
categories: android
typora-root-url: ..
---

CoordinatorLayout是协调者布局，是协调管理子View与子View之间的交互。

CoordinatorLayout的功能：
1. 处理子View之间依赖下的交互
```java
boolean layoutDependsOn
boolean onDependentViewChanged
void onDependentViewRemoved
```
2. 处理子View之间的嵌套滑动
```java
boolean onStartNestedScroll
void onNestedScrollAccepted
void onNestedPreScroll
void onNestedScroll
boolean onNestedPreFling
boolean onNestedFling
void onStopNestedScroll
```
注：与NestedScrollingView的嵌套滑动相比有什么不同？
解答：NestedScrolling嵌套滑动机制有局限性，只能处理child与parent两者的嵌套滑动，是1对1的关系。而CoordinatorLayout里的管理嵌套滑动是任何View，是1对多关系。

3. 处理子View的测量与布局
```java
boolean onMeasureChild
boolean onLayoutChild
```
4. 处理子View的事件拦截与响应
```java
boolean onInterceptTouchEvent
boolean onTouchEvent
```

注：onInterceptTouchEvent只有在ViewGroup中才有，但是Behavior可以让View也响应实现onInterceptTouchEvent方法。

上述的四种功能都建立在CoordinatorLayout中提供的一个<strong>`Behavior`插件</strong>上，上述列举的方法就是对应的behavior里的方法。
使用不同的Behavoir可以实现不同的行为。

## CoordinatorLayout下依赖交互原理

当CoordinatorLayout中子控件dependency的位置，大小发生变化时，那么CoordinatorLayout内部会通知所有依赖dependency的控件，并调用对应声明的Behavior，告知其dependency发生变化。通过`layoutDependsOn`来判断依赖控件，通过`onDependentViewChanged\onDependentRemove`来通知处理。这些都由Behavoir来处理。

![dependency原理](/assets/images/2020-08-13/dependency原理.png)

## CoordinatorLayout下嵌套滑动

```java
public class CoordinatorLayout extends ViewGroup implements NestedScrollingParent2,
        NestedScrollingParent3 
```
CoordinatorLayout实现了NestedScrollingParent2/NestedScrollingParent3接口，那么当控件内部有实现NestedScrollingChild接口的子控件发生scroll或fling时，会将事件分发给CoordinatorLayout，CoordinatorLayout又会将事件传递给所有的Behavior.然后Behavior中就实现子控件的嵌套滑动。

![CoordinatorLayout嵌套滑动原理](/assets/images/2020-08-13/CoordinatorLayout嵌套滑动.png)

问题1: 产生scroll/fling事件的控件必须是CoordinatorLayout的直接子View吗？
解答：不是的。当子View实现NestedScrollingChild接口时，在执行onStartNestedScroll时，会向上循环找出实现了NestedScrollingParent的接口的父View，而此时的父View是CoordinatorLayout,也就是说不仅仅直接子View可以发送scroll/fling事件。

问题2: 响应Behavior的控件必须是CoordinatorLayout的直接子View吗？
解答：是，必须是直接子View。

## CoordinatorLayout下子控件的测量与布局

CoordinatorLayout主要负责的是子控件之间的交互，内部控件的测量和布局都非常简单。在特殊情况下，如子控件需要处理宽高与布局的时候，可以交由Behavior内部的onMeasureChild和onLayoutChild来处理。

![CoordinatorLayout下子控件测量与布局](/assets/images/2020-08-13/CoordinatorLayout下子控件测量与布局.png)

当ViewGroup对子View进行测量和布局时，直接调用onMeasure和onLayout方法。
问题：为什么在CoordinatorLayout里要通过Behavior去回调子控件的测量和布局？在什么情况下会回调onMeasureChild和onLayoutChild？

## CoordinatorLayout下子控件事件拦截与消费

同理的，如果子控件需要拦截与消费事件，也是交由Behavior里的onInterceptTouchEvent与onTouchEvent进行处理。

![CoordinatorLayout下子控件事件拦截与消费](/assets/images/2020-08-13/CoordinatorLayout子控件事件分发与拦截.png)

## CoordinatorLayout源码解读

问题1: 为什么在依赖的控件下设置一个behavior，DependedView位置发生改变的时候就能通知依赖方？
解答：
在CoordinatorLayout的onAttachedToWindow方法中，创建视图树的监听ViewTreeObserver.onPreDrawListener,在调用onPreDraw时执行onChildViewsChanged()方法，而这个方法中，参数有三个（EVENT_PRE_DRAW, EVENT_NESTED_SCROLL, EVENT_VIEW_REMOVED），在进行变化后都会调用onChildViewsChanged()方法，通过双重for循环来确定哪个是被依赖方，并且发生变化后进行通知依赖方。

EVENT_PRE_DRAW：OnPreDrawListener的onPreDraw方法
EVENT_NESTED_SCROLL： onNesterPreScroll,onNestedScroll,onNestedFling方法
EVENT_VIEW_REMOVED：构造函数中super.setOnHierarchyChangeListener(new HierarchyChangeListener())，监听了View被移除的情况onChildViewRemoved中调用onChildViewsChanged(EVENT_VIEW_REMOVED);

当depedency View位置发生改变，那么一定会重绘，视图树一定会通知onPreDrawListener

问题2: Behavior在哪实例化的？
解答：
在CoordinatorLayout的LayoutParams中，behavior是通过parseBehavior()方法来将xml中的类取到进行来实例化。

问题3: CoordinatorLayout是如何区分谁依赖谁的？
解答：
在onMeasure方法中收集到mDependencySortedChildren中，有向无环图（DirectedAcyclicGraph）

问题4: 什么时候需要重写onMeasureChild?

问题5: 什么时候需要重写onLayoutChild?

## View生命周期简介

![View生命周期简介](/assets/images/2020-08-13/View生命周期简介.png)

## ViewTreeObserver介绍（视图树监听者/观察者）

ViewTreeObserver注册一个观察者来监听视图树，当视图树布局、视图树焦点、视图树将要绘制、视图树滚动等发生改变时，ViewTreeObserver都会收到通知。

ViewTreeObserver不能被实例化，通过View.getViewTreeObserver()来获取。

![ViewTreeObserver常用内部类](/assets/images/2020-08-13/ViewTreeObserver常用内部类.png)

dispatchOnPreDraw(): 通知观察者绘制即将开始，如果其中某个观察者返回true，那么绘制将会取消，并且重新安排绘制。
如果想在View Layout或者View hierarchy还未依附到Window时，或者Window处于GONE状态时强制绘制，可以手动调用这个方法。

## 自定义ViewGroup如何增加子View的属性

当自定义ViewGroup后，需要给子View进行增加属性，那么需要重写ViewGroup.LayoutParams，可直接继承MarginLayoutParams，CoordinatorLayout里面的LayoutParams就是继承了MarginLayoutParams.

## CoordinatorLayout对子View的管理

一般ViewGroup获取子View使用getChildAt(),而CoordinatorLayout使用mDependencySortedChildren集合将子View进行整理。
```java
private final List<View> mDependencySortedChildren = new ArrayList<>();
private final DirectedAcyclicGraph<View> mChildDag = new DirectedAcyclicGraph<>();
```
`DirectedAcyclicGraph`是一种图的数据结构，有向五环图，因为`CoordinatorLayout`中子View具有依赖关系，可能一对多，所以使用`DirectedAcyclicGraph<View>`将这个依赖关系进行管理，之后将管理后的View添加到`mDependencySortedChildren`中。
在onMeasure中将子View添加到`mDependencySortedChildren`集合中