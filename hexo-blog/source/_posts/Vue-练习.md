---
title: Vue-练习
date: 2017-06-13 23:19:41
tags: Vue.js-练习
---

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Vue--练习</title>
    <script src="https://unpkg.com/vue/dist/vue.js"></script>
</head>
<body>
                
<div id="vm">
            <fieldset>   <!--U型框-->
                <legend>  
                    Create New Person     <!--U型框标题-->
                </legend>
                <div class="inputmessage">
                    <label>姓名:</label>
                    <input type="text" v-model="newperson.name"/>
                </div>
                <div class="inputmessage">
                    <label>年龄:</label>
                    <input type="number" v-model.number="newperson.age"/>    <!--只能输入数值类型-->
                </div>
                <div class="inputmessage">
                    <label>性别:</label>
                    <select v-model="newperson.sex">
                    <option>Male</option>
                    <option>Female</option>
                </select>
                </div>
                <div class="inputmessage">
                    <label>创建按钮:</label>
                    <button @click="createperson">Create</button>
                </div>
        </fieldset>
        <table>
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Age</th>
                    <th>Sex</th>
                    <th>Delete</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(person,index) in people">
                    <td>{{ person.name }}</td>
                    <td>{{ person.age }}</td>
                    <td>{{ person.sex }}</td>
                    <td :class="'text-center'"><button @click="deleteperson(index)">Delete</button></td>
                </tr>
            </tbody>
        </table>
        </div>
    </body>
    <script>
        var vm = new Vue({
            el: '#vm',
            data: {
                newperson: {
                    name: '',
                    age: 0,
                    sex: 'Male'
                },
                people: [{
                    name: 'Jack',
                    age: 23,
                    sex: 'Male'
                }, {
                    name: 'Lucy',
                    age: 26,
                    sex: 'Female'
                }, {
                    name: 'Frank',
                    age: 32,
                    sex: 'Male'
                }, {
                    name: 'Rose',
                    age: 18,
                    sex: 'Female'
                }]
            },
            methods:{
                createperson: function(){
                    this.people.push(this.newperson);       //添加
                    // 添加完newPerson对象后，重置newPerson对象
                    this.newperson = {name: '', age: 0, sex: 'Male'}
                },
                deleteperson: function(index){
                    // 删一个数组元素
                    this.people.splice(index,1);          //删除
                }
            }
        })
    </script>
</html>
```


![image.png](http://upload-images.jianshu.io/upload_images/1393082-e5fd5a41f4e6de7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


