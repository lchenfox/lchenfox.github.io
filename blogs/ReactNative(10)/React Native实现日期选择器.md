---
title: React Native日期选择器
date: 2020-06-18 21:21:45
categories: React Native
tags: React Native, react-native-date-picker, react-native-common-date-picker，datepicker, 日期选择器
---

# React Native日期选择器

**iOS和Android通用日期选择器：**[react-native-common-date-picker](https://github.com/lchenfox/react-native-common-date-picker)（如果你觉得还不错，记得给个 ⭐️⭐️⭐️）。

## 效果

**日期选择器截图**

|   Android 1 | Android 2  | Android 3  | Android 4  |                                                                                                                          
| ----------- | ------------ | -------------- | ---- |
| ![Android 1.png](https://i.loli.net/2020/06/12/BcqVmEsYAlSdRvz.png)    | ![Android 2.png](https://i.loli.net/2020/06/12/4L86ouwz1PNrdIG.png)  | ![Android 3.png](https://i.loli.net/2020/06/12/KSGh74QTdDn9wlH.png) | ![Android 4.png](https://i.loli.net/2020/06/12/k3NbBnzmRQ65ZJS.png)

|   iOS 1 | iOS 2  | iOS 3  | iOS 4  |                                                                                                                          
| ----------- | ------------ | -------------- | ---- |
| ![iOS 1.png](https://i.loli.net/2020/06/12/nvd24jc1zGrAhLW.png)    | ![iOS 2.png](https://i.loli.net/2020/06/12/DgLFt79Uz38P2hv.png)  | ![iOS 3.png](https://i.loli.net/2020/06/12/wehKmGE3s5aoWtJ.png) | ![iOS 4.png](https://i.loli.net/2020/06/12/T5QjBLpYH7lNrUm.png)

**日历列表截图**

|   Android 1 | Android 2  | Android 3  | Android 4  |                                                                                                                          
| ----------- | ------------ | -------------- | ---- |
| ![Android1.png](https://i.loli.net/2020/06/06/ldkZV3NpQivuL8z.png)    | ![Android2.png](https://i.loli.net/2020/06/06/Xhk2uwjvszreHTg.png)  | ![Android3.png](https://i.loli.net/2020/06/06/iBPDeptU4kFLjN2.png) | ![Android4.png](https://i.loli.net/2020/06/06/tyALsbHZfGVzrJw.png)

|   iOS 1 | iOS 2  | iOS 3  | iOS 4  |                                                                                                                          
| ----------- | ------------ | -------------- | ---- |
| ![iOS1.png](https://i.loli.net/2020/06/06/LwEa476VAQ8kgTC.png)    | ![iOS2.png](https://i.loli.net/2020/06/06/K6h21JlyTspo7gq.png)  | ![iOS3.png](https://i.loli.net/2020/06/06/Pw1DnIkMjtve5NC.png) | ![iOS4.png](https://i.loli.net/2020/06/06/nUuI1bSxayiTkZe.png)

|   日期选择器GIF | 日历列表GIF  |                                                                                                                        
| ----------- | ------------ | 
| ![DatePicker.gif](https://i.loli.net/2020/06/15/8fyQB3WA1KMPhzt.gif) | ![CalendarList.gif](https://i.loli.net/2020/06/15/baRr4Ymh8qBKoZt.gif) |

## 前言

一直以来，都会因为`RN`的日期选择器使用而浪费时间和精力。首先，`RN`官方提供的日期选择器在`iOS`和`Android`上并不统一，尤其是在`Android`上，展示的是**钟表**的形式，作为一个`iOS`开发者来说，简直难以接受。

因此，对于`RN`开发来说，[RN官方 - DatePickerIOS](https://reactnative.dev/docs/datepickerios) 和 [RN官方 - DatePickerAndroid](https://reactnative.dev/docs/datepickerandroid) 的不统一，这点就让很多人弃用官方提供的组件和`API`，那有没有现成的可以使用的日期选择器呢？

**注：上面官方提供的日期选择器目前已经由RN社区统一维护，[链接：@react-native-community/datetimepicker](https://github.com/react-native-community/datetimepicker)。**

现成的日期选择器自然是有的，比如[react-native-modal-datetime-picker](https://github.com/mmazzarolo/react-native-modal-datetime-picker)和官方提供的类似，可以说只是一层封装。再比如另一个较为符合需求的[react-native-date-picker](https://github.com/henninghall/react-native-date-picker)。然而，这些库要么基于官方做了一层封装，要么就是不能灵活地调整一些必要的配置，比如我们希望只显示**年-月**或者调整顺序的**月-日-年**、**日-月-年**等等。

笔者的目的就是为了要让**需求**无处遁形，无论你怎么调整顺序、需要调整颜色、距离、字体大小等，我只需要配置一下参数就可以满足产品经理的**“无理要求”**，也因此才有了本文的重复造轮子。

## 为什么要推荐react-native-common-date-picker

主要有以下几点：

- 支持`iOS和Android`，样式统一
- 安装和使用方便，纯JavaScript实现，无需`link`
- 灵活性高，参数配置丰富，无论你只想显示**年-月-日**还是**月-日-年**、**日-月-年**、**年-月**等都可以
- 支持日期选择器和日历📆两种不同类型的日期选择
- 性能高，使用最基础最原始的“看似笨拙”的方法来提升性能（可以看源码里面有注释）

## 安装

本库是纯`JS`实现，因此安装依赖非常简单，一步到位：

```
npm install react-native-common-date-picker
```

## 使用

#### 日期选择器

```
import {DatePicker} from "react-native-common-date-picker";

<DatePicker
    confirm={date => {
        console.warn(date)
    }}
/>
```

#### 日历选择列表📆

```
import {CalendarList} from "react-native-common-date-picker";

<Modal animationType={'slide'} visible={this.state.visible}>
     <CalendarList
          containerStyle={{flex: 1}}
          cancel={() => this.setState({visible: false})}
          confirm={data => {
              this.setState({
                   selectedDate1: data[0],
                   selectedDate2: data[1],
                   visible: false,
              });
          }}
     />
</Modal>
```

更多的使用例子，大家自行查看[GitHub: react-native-common-date-picker仓库](https://github.com/lchenfox/react-native-common-date-picker)，源码里面也有很全面的注释，非常容易配置和使用。



