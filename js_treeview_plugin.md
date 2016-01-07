---
layout: post
title: 自定义js插件
permalink: js_treeview_plugin.md
description: treeViewPlugin
date: 2016-01-07 13:59:55 +08:00
tags: ["JavaScript", "Plugin", "插件"]
---

# JavaScript之树列表插件实现

treeViewPlugin，一个可以生成任意多级树列表的js插件，使用了jquery事件委托处理跨浏览器添加事件监听及DOM操作实现需求。[查看插件源码点此](https://github.com/codingplayboy/javascriptDemo/tree/master/treeViewPlugin)

## API说明

### 插件定义

```

	var treeViewPlugin = (function() {  //插件定义、封装
		//插件实现
	}());
```

### 插件API

```
	
	return {
		initModule: initModule,  //初始化树列表
		addParents: addParents,  //给树列表添加父级菜单
        	addChild: addChild,  //给树列表添加子级菜单
        	addChildNode: addChildNode  //给树列表添加无子级的子级菜单
	};
```

#### 初始化

```

	/**
	 * [initModule 初始化树列表]
	 * @param  {[type]} $append_target [树列表容器]
	 *	 $('.treeviewCont')
	 * @return {[type]}                [函数返回值]
	 */
	initModule = function($append_target) {
		//具体实现
	};
```

#### 添加父级菜单

```

	/**
	 * [addParents 添加父级菜单
	 * @param {[Array]} _data [菜单数据]
	 * [{
	 * 		id: 菜单id,
	 * 		name: 菜单显示值
	 * 
	 * }]
	 */
	addParents = function(_data) {
		//具体实现
	};
```

#### 添加子级菜单

```

	/**
     * [addChild 添加子级菜单]
     * @param {[Array]} _data [菜单数据]
     * [{
     * 		id: 菜单id,
     * 		name: 菜单显示值,
     * 		pId: 父级菜单id
     * }]
     */
    addChild = function(_data) {
    	  //具体实现
    };
```

#### 添加无子级的菜单

```

	/**
     * [addChildNode 添加无子级子级菜单]
     * @param {[Array]} _data [菜单数据]
     * [{
     * 		id: 菜单id,
     * 		name: 菜单显示值,
     * 		pId: 父级菜单id
     * }]
     */
     addChildNode = function(_data) {
     	  //具体实现
     };
```

## demo展示

```

	<!DOCTYPE html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<title>Treeview Plugin Demo</title>
		<link rel="stylesheet" href="./css/js-treeview-plugin.css">
		<style>
			.container {
				width: 100%;
				height: 100%;
				text-align: center;
			}
			.treeviewCont {
				width: 300px;
				height: 300px;
				margin: 0 auto;
				margin-top: 100px;
			}
			
		</style>
	</head>
	<body>
		<h1>Treeview Plugin Demo</h1>
		<div class="container">
			<div class="treeviewCont"></div>
		</div>
		<script>
			$(function() {
				var tree = $('.treeviewCont');
				treeViewPlugin.initModule(tree);
				treeViewPlugin.addParents([
		                {
		                    name: 'Betty',
		                    id: 'id_01'
		                },
		                {
		                    name: 'Mike',
		                    id: 'id_02'
		                },
		                {
		                    name: 'Pebbles',
		                    id: 'id_03'
		                },
		                {
		                    name: 'Wilma',
		                    id: 'id_04'
		                }
		            ]);
		            treeViewPlugin.addChild([
		                {
		                    pId: 'id_01',
		                    name: 'Betty',
		                    id: 'id_01_01'
		                },
		                {
		                    pId: 'id_01',
		                    name: 'Mike',
		                    id: 'id_01_02'
		                },
		                {
		                    pId: 'id_03',
		                    name: 'Pebbles',
		                    id: 'id_03_01'
		                },
		                {
		                    pId: 'id_04',
		                    name: 'Wilma',
		                    id: 'id_04_01'
		                }
		            ]);
		            treeViewPlugin.addChildNode([
		                {
		                    pId: 'id_01',
		                    name: 'Btrey',
		                    id: 'id_01_001'
		                },
		                {
		                    pId: 'id_01',
		                    name: 'Hans',
		                    id: 'id_01_002'
		                },
		                {
		                    pId: 'id_03',
		                    name: 'wekous',
		                    id: 'id_03_001'
		                },
		                {
		                    pId: 'id_04',
		                    name: 'Wimaka',
		                    id: 'id_04_001'
		                }
		            ]);
			});
		</script>
	</body>
	</html>
```

**此插件只是初步实现，更多功能待完善，实际使用可根据实际需求做相应修改，欢迎大家一起分享、交流、学习。**
*[查看插件源码点此](https://github.com/codingplayboy/javascriptDemo/tree/master/treeViewPlugin)
*
