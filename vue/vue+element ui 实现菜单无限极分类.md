---
title: vue+element ui 实现菜单无限极分类
date: 2017/10/26
categories:
- vue
description: 通过实现一个element ui的菜单组件,再递归该组件,实现菜单的无限极分类
---

### 需后端返回数据结构如下:
> 后端实现方法可参考: [菜单栏数据递归实现](http://oryxtc.win/php/php/php%E4%BB%A3%E7%A0%81%E5%9D%97/#菜单栏数据递归实现 )

```php
[
    {
        "id": 1,
        "parent_id": 0,
        "menu_name": "第一级菜单 1",
        "sorting": 0,
        "node": [
            {
                "id": 2,
                "parent_id": 1,
                "menu_name": "第二级菜单 1-1",
                "sorting": 0,
                "node": [
                    {
                        "id": 3,
                        "parent_id": 2,
                        "menu_name": "第三级菜单 1-1-1",
                        "sorting": 1
                    }
                ]
            }
        ]
    },
    {
        "id": 4,
        "parent_id": 0,
        "menu_name": "第一级菜单 2",
        "sorting": 0,
        "node": [
            {
                "id": 5,
                "parent_id": 4,
                "menu_name": "第二级菜单 2-1",
                "sorting": 0
            }
        ]
    }
]
```

### 新建MenuBar.vue文件,实现获取后端数据及布局功能
```html
<template>
    <div>
        <el-radio-group v-model="isCollapse" style="margin-bottom: 20px;">
            <el-radio-button :label="false">展开</el-radio-button>
            <el-radio-button :label="true">收起</el-radio-button>
        </el-radio-group>
        <el-menu class="menu-Bar" :collapse="isCollapse">
            <MenuTree :menuData="this.menuData"></MenuTree>
        </el-menu>
    </div>
</template>
<script>
  import MenuTree from '@/components/MenuTree'

  export default {
    data () {
      return {
        isCollapse: false,
        menuData: []
      }
    },
    props: ['apiUrl'],
    created: function () {
      this.getMenu()
    },
    methods: {
      getMenu: function () {
        this.$http.get(this.apiUrl + 'menu').then(function (response) {
          this.menuData = response.data
        }, function (error) {
          console.log(error)
        })
      }
    },
    components: {
      'MenuTree': MenuTree
    }
  }
</script>
<style>
    .menu-Bar:not(.el-menu--collapse) {
        width: 200px;
        min-height: 400px;
    }
</style>
```

### 新建MenuTree.vue 实现菜单栏列的递归渲染
```html
<template>
    <div>
        <template v-for="value in this.menuData">
            <el-submenu index="value.id" v-if="value.node">
                <template slot="title">
                    <i class="el-icon-message"></i>
                    <span slot="title">{{value.menu_name}}</span>
                </template>
                <MenuTree :menuData="value.node"></MenuTree>
            </el-submenu>
            <el-menu-item index="value.id" v-else>
                <i class="el-icon-message"></i>
                <span slot="title">{{value.menu_name}}</span>
            </el-menu-item>
        </template>
    </div>
</template>

<script>
  export default {
    props: ['menuData'],
    name: 'MenuTree'
  }
</script>
```

### 最后实现效果为
![](http://ooqid2far.bkt.clouddn.com/myblog/vue+element%20ui%E8%8F%9C%E5%8D%95%E9%80%92%E5%BD%92%E5%AE%9E%E7%8E%B0.png)
