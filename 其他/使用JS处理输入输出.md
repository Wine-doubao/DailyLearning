## JS（V8）

### 读取API

1. 读取一行输入
   - `read_line()`
   - 至多1024个字符，如果还未达到1024个就遇到回车或结束符，提前结束。
2. 读取多行
   - `while((line=read_line())!='')`
   - `gets(n)`：n为读取至多n个字符，如果未达到n个时遇到回车或结束符，会提前结束，但是**回车符会包含在返回值中**！可以使用gets(n).trim()。
3. 读取一个(长)整数
   - `readInt()`
4. 读取一个浮点型
   - `readDouble()`

### 输出API

1. 不带回车的输出
   - `printsth(sth,...)`
   - 往控制台输出是sth，如果有多个参数，空格分隔；最后不加回车。
2. 带回车的输出
   - `print(sth,...)`
   - `console.log(sth,...)`

### 示例

```javascript
let arr = [];
let line;
while((line = read_line()) != "") {// 将多行数字存入数组中
  arr.push(line.split(' ').map(v=>parseInt(v)));
}

/* 如果输入的数组中有中括号和逗号，如：
[[3,2,3],
 [1,6,5],
 [7,8,9]]
*/
let arr = [];
let line;
while ((line = read_line()) != "") {
  arr.push(line.replace(/\]\,/g, "").replace(/ /g, "").replace(/\[/g, "").replace(/\]/g, "").split(",").map(v=>parseInt(v)));
}

// 输出数组或矩阵
let arr = [[1,2,3],[4,5,6]]
console.log(arr.toString())// 输出是：1,2,3,4,5,6  这样就错了，没有中括号
console.log(JSON.stringify(arr));// 这样是对的
// 也可以通过拼接字符串实现
```



## JS（Node)

- 建议使用nodejs内置模块：`readline`
- 也可以使用`process.stdin`、`process.stdout`

### 示例

```javascript
const readline = require('readline');
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

//单行输入
rl.on('line',function(data){
  var result= data.split(' '); //获取第一行的内容，存为数组
  console.log(result);
})

//多行输入 
var k = -1; //初始化行数为-1，表示还未开始
var rows = []; //用于存储每行的输入
rl.on('line',function(data){
  if(k === -1){
    k = parseInt(data.trim()); //读取第一行，获取接下来输入的行数
  }else{
    rows.push(data.trim()); //将每次输入的行数据存入
    if(k === rows.length){ //当输入的行数等于设定的k值时，开始逻辑处理
      console.log(rows); //输出结果
    }
  }
});
/* 多行输入示例
4
1 4
2 3
3 6
5 8
输出：['1 4', '2 3', '3 6', '5 8']
*/
```

