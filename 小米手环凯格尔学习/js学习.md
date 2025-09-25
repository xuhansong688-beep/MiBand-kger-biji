变量let可变，const不可变
string字符 number数字没有浮点概念 bollean正确与否 null数字被定义但是为空 undefine 不可被定义
在后加.split（“”）可以通过引号中的内容进行分割
数组是中括号加双引号加逗号
打印中加点中括号数字是第几个
 数组前面加：bbb.unshift("asdasd")
 后面：aaa.push("1qasd") 删除是.pop()
 log(array.isarray("))出现对错
 面向对象，转换json形式：const todos = [
        {
                id: 1,
                test : "aushdhasd",
                isss:true,
        },
        {
                id: 2,
                test : "aushdhasd",
                isss:true,
        },
        {
                id: 3,
                test : "aushdhasd",
                isss:true,
        },
];
const todojoso = JSON.stringify(todos);
console.log(todos);
console.log(todojoso);

if判断== 是都行 === 是必须一个
for(let todo of todos){
        console.log(todo.test);
}