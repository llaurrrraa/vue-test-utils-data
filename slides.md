---
# You can also start simply with 'default'
theme: default
# background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Vue 單元測試 vue-test-utils
info: |
  ## Vue 單元測試 vue-test-utils 
  測試內部資料更新
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: true
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
---

# Vue 單元測試 vue-test-utils

第 3 章：測試內部資料的更新<br/>
( presentation by Laura )

<div class="abs-br m-6 text-xl">
  <a href="https://github.com/llaurrrraa/introduce_slidev" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

<style>

h1 {
  font-weight: bold;
  letter-spacing: 1.05px;
  font-size: 2.25rem !important;
  margin-bottom: 0.5rem;
}
.dark h1 {
  color: #fff !important;
}
.light h1 {
  color:rgb(0, 0, 0);
}

.slidev-layout h1 + p {
  opacity: 0.75;
}
.slidev-layout a.slidev-icon-btn:hover {
  color: #000;
}
.slidev-layout a:hover {
  padding: 0.5rem;
}
</style>


--- 
layout: two-cols
layoutClass: gap-16
---

## 大綱

::right::

<Toc text-sm minDepth="1" maxDepth="2" />

<style>
a div p {
  font-size: 1.25rem !important;
}
</style>

---
# transition: fade-out
---

## review

<br/>

| 方法             | 作用                       | 差異點                               |
| --------------- | -------------------------- |------------------------------------ |
| mount ()         |  完整掛載元件（包含所有子元件） | 會渲染子元件，適合整體測試              |
| shallowMount ()  |  淺層掛載元件（不渲染子元件）   | 子元件會被 stub 替換，適合測試單一元件   |

<br/>
<br/>

| 方法              |  找不到元素時                                   | 適用場景                             |
| ---------------- | ---------------------------------------------- |----------------------------------- |
| find (selector)  |  不報錯，回傳空 Wrapper，需要 .exists() 來檢查     | 回傳空的 Wrapper（不報錯）            |
| get (selector)   |  會直接拋出錯誤                                  | 確保元素必須存在，減少額外檢查          |

<style>
li {
  font-size: 1.025rem;
  letter-spacing: 0.85px;
}
.light strong {
  color: #fff !important;
  padding: 0 0.5rem !important;
  letter-spacing: 0.5px;
  background-color: #000;
}
strong {
  color: #000 !important;
  padding: 0 0.5rem !important;
  letter-spacing: 0.5px;
  background-color: #fff;
  border-radius: 3px;
}
</style>

<!--
首先他可以用 markdown 語法就做完 ppt，直接用 css 調整 layout

再來是社群裡有不少模板可以套用，只要 npm 就可以使用

可以指定一行或多行程式碼片段的 highlight
-->

---
layout: two-cols
layoutClass: gap-4
---

## 01 - 測試 data & props

▎　BoxData.spec.js 

```js {6-17}{lines:true}
import { mount } from "@vue/test-utils";
import CountList from "@/components/CountList";
import { ref } from "vue";

describe("CountList.vue", () => {
  const data = {
    props: {
      count: 5
    },
    setup(props) {
      const listIdx = ref(5);
      return { props, listIdx }
    }
  };

  it("set data value", () => {
    const wrapper = mount(CountList, data); // data 會去覆蓋掉原本 props 的值
    expect(wrapper.find("h1").text()).toBe(`目前的人數是5人`);
  });
});
```

::right::

▎　Index.vue

```html {all}{lines:true}
<template>
  <div>
    <h1>目前的人數是 {{ listIdx }} 人</h1>
    <h2>存款有 {{ props.count }} 元</h2>
  </div>
</template>

<script>
import { ref } from "vue";
export default {
  props: {
    count: {
      type: Number,
      default: 0
    }
  },
  setup(props) {
    const listIdx = ref(2)
    return { listIdx, props }
  }
};
</script>
```

<style>
a div p {
  font-size: 1.25rem !important;
}
</style>

---

## 02 - 手動設定資料： setData()

<br/>

```js {7-8}{lines:true}
import { mount } from "@vue/test-utils";
import BoxData from "@/components/BoxData";

// https://github.com/vuejs/test-utils/issues/1058
describe("BoxData.vue", () => {
  it("setData Value change isOpen", async () => {
    const wrapper = mount(BoxData);
    await wrapper.setData({ isOpen: true });
    expect(wrapper.find(".box_data").isVisible()).toBe(true);
  });
})
```
<br />

- setData 是非同步的，記得加 `async`, `await`
- setData 尚未支援 vue3 的 composition-api

<style>
.footnotes-sep {
  @apply mt-5 opacity-10;
}
.footnotes {
  @apply text-sm opacity-75;
}
.footnote-backref {
  display: none;
}
</style>

<!--
除了直接給預設值，也可以用mount提供的方法，setData去設定值。

setData 是非同步的，會回傳 promise，要記得補上 async 和 await

上一個 data 變數是在初始化的時候覆蓋值，

setData 是初始 component 後再去賦予值，需要等 DOM 更新，所以需要做非同步的處理
-->

---
level: 2
---

## 03 - 事件 click： trigger()

<br/>

```js {7}{line: true}
import { shallowMount } from "@vue/test-utils";
import AddCount from "@/components/AddCount";

describe("AddCount.vue", () => {
  it("click add count", async () => {
    const wrapper = shallowMount(AddCount);
    await wrapper.find(".add_btn").trigger("click");
    expect(wrapper.find(".count_title").text()).toBe("1");
    await wrapper.find(".add_btn").trigger("click");
    expect(wrapper.find(".count_title").text()).toBe("2");
  });
});
```

<br/>

- trigger 也是非同步的，需加上 `async` `await`
- 用 trigger 去觸發 click 事件

<!--
先找到按鈕的 class，然後用 trigger 去觸發 click 事件
-->

---

## 04 - 手動設定 props： setProps()

<br/>

```js {7}{lines: true}
import { mount } from "@vue/test-utils";
import ContentBox from "@/components/ContentBox";

describe("ContentBox.vue", () => {
  it("setData Value change isOpen", async () => {
    const wrapper = mount(ContentBox);
    await wrapper.setProps({ isOpen: false });
    expect(wrapper.find(".box").isVisible()).toBe(false);
  });
})
```

<br />

- setProps() 一樣是非同步，需加上 `async` `await`
- 測試可以用 `defineProps`

<!--
除了直接覆蓋值，也可以用 setProps 去覆蓋 props，

在影片裡，mike說只能用 option api 的寫法，

不過自己嘗試後，發現用 defineProps的寫法也可以測試
-->


---
# transition: slide-up
---

## 05 - 手動設定表單value： setValue()

<br/>

```js {7-11}{lines: true}
import { mount } from "@vue/test-utils";
import InputBar from "@/components/InputBar";

describe("InputBar.vue", () => {
  it("input set Value", () => {
    const wrapper = mount(InputBar);
    wrapper.find("input.name").setValue("Mike");
    expect(wrapper.find("input.name").element.value).toBe("Mike");

    wrapper.find("input.email").setValue("1208966@gmail.com");
    expect(wrapper.find("input.email").element.value).toBe("1208966@gmail.com");
  });
});
```

<br/>

- 主要用於 表單輸入元素 ( input, textarea, select ) 
- 通常使用在 v-model 綁定的 data 屬性上
- 用於設定表單元素的值，會觸發 Vue 的響應式更新機制
- 會自動等待 DOM 更新

<style>
.slidev-page-8 {
  position: relative;
}
.link {
  position: absolute;
  bottom: 1rem;
}
</style>

<!--
測試表單的值，可以用 setValue，

因為 v-model 會雙向綁定到 data 或 props 上，vue 的響應式機制就能夠去更新 UI
-->

---
# transition: slide-up
---

## 05 - setValue() 自動更新

<br/>

```js {all}{lines: true}
import { mount } from "@vue/test-utils";
import InputBar from "@/components/InputBar";
import { nextTick } from 'vue';

describe("InputBar.vue", () => {
  it("input set Value", async () => {
    const wrapper = mount(InputBar);
    const input = wrapper.find('input.name')
    input.element.value = 'Hello'               // 設定輸入框的值
    input.trigger('input')                      // 觸發 input 事件，啟動 Vue 的響應式更新
    await nextTick()                            // ✅ Vue 內部等待 DOM 更新完成
    expect(wrapper.find("input.name").element.value).toBe("Hello");
  });
});
```

<br/>


<style>
.dark strong {
  background-color: #000;
}
strong {
  padding: 0 0.5rem;
  background-color: #fff;
  border-radius: 5px;
}
</style>

---
layout: two-cols
layoutClass: gap-4
---

## 05 - computed

▎　setProps
<br/>

```js {4}{lines: true}
describe("InputBar.vue", () => {
  it("props computed change input value", async () => {
    const wrapper = mount(InputBar);
    await wrapper.setProps({ thousand: '3012300'});
    expect(wrapper.find("input.num").element.value).toBe("3,012,300");
  });
});
```
<br/>

- 如果是經計算後的值建議用 setProps

:: right ::

▎　直接修改變數

<br/>

```js {4-5}{lines: true}
describe("InputBar.vue", () => {
  it("props computed change input value", async () => {
    const wrapper = mount(InputBar);
    wrapper.vm.thousand = 3012300;
    await nextTick();
    expect(wrapper.find("input.num").element.value).toBe("3,012,300");
  });
});
```

<!-- 
因為 computed 是 read-only，不能夠直接修改

不應該對 computed 使用 setValue，改去修該他依賴的 data 或 props

那既然都要改 props 的話就建議直接使用 setProps
 -->


--- 
# transition: slide-up
---

## 06 - emitted() 的觸發

```js {6}{lines: true}
describe("Combination.vue", () => {
  it("check emit string", async () => {
    const wrapper = mount(Combination);
    await wrapper.setProps({ thousand: 3012300 });
    await wrapper.find("button").trigger("click");
    expect(wrapper.emitted('Combination')[0][0]).toBe("3,012,300");
  });
});
```

<style>
h1 {
  font-weight: bold;
  letter-spacing: 1.5px;
  font-size: 1.75rem;
}
svg {
  border: 1px solid #fff;
  border-radius: 10px;
  margin-top: 1rem;
}
</style> 

<!-- 測試 emit 後的資料 -->


---
# layout: center
# class: text-center
---

## 總結

<br/>

| 方法              |  用途                                | 是否需要 await？               |
| ---------------- | ------------------------------------ |------------------------------|
| setValue()       |  設定 表單輸入值                       | ⚠️ 建議加 await，但可不加       |
| setProps()       |  設定 props                          | ✅ 需要 await                 |
| setData()        |  設定 data()（僅適用於 Options API）   | ✅ 需要 await                 |
| emitted()        |  檢查 component 是否發送事件           | ❌ 不需要 await               |
| emitted()        |  觸發 DOM 事件（點擊、輸入等）	         | ✅ 需要 await                 |