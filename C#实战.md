## .Net core 入门

### .Net Framework 与 .Net Core的不同

1. 在`.Net Framework`中，项目中的所有代码都要添加到`*.csproj`文件中，如果一个C#文件放在项目文件夹下，但是没有被添加到`*.csproj`中，那么这个文件是不会被编译的。       在`.Net Core`中，项目中的文件不用添加到`*.csproj`文件中，项目中的文件默认都被自动包含到项目中，除非**手动**排除。

2. `.Net Core`项目没有`App.config` 或者` Web.config`

## .Net core重难点

### 新语法

#### 顶级语句

```c#
Console.WriteLine("Hello World"); // 不需要Main  可以直接运行
```

- 顶级语句语法的功能是让编译器帮助我们简化工作
- 同一个项目中只能有一个文件具有顶级语句

##### 全局Using指令

通常创建一个专门用来编写全局`using`代码的`C#`文件。将经常需要用的命名空间声明到这个文件中，如下。

```c#
global using System.Text.Json
global using Microsoft.Data.Sqlite
```

还可以在`*.csproj`文件中加入`<ImplicitUsings>enable</ImplicitUsings>`，编译器根据项目类型自动为项目隐式地增加对`System`、`System.Linq`等常用命名空间的引入。

##### using声明

`using`简化**非托管资源**的释放

传统用法  导致嵌套

```c#
using(var conn = new SqlConnection(connStr)){
    conn.Open();
    using(var cmd = conn.CreateCommand){
        cmd.CommandText = "select * from T_Artitles";
       using(SqlDataReader reader = cmd.ExecuteReader()){
           while(reader.Read()){
               
           }
       }
    }
}
```

`C#8.0 `之后   变量声明前加`using`关键字，当代码执行离开被`using`修饰的变量作用域的时候，变量指向的对象的`Dispose`方法就会被调用

```c#
using var conn = new SqlConnection(connStr);
conn.Open();
using var cmd = conn.CreateCommand;
cmd.CommandText = "select * from T_Artitles";
using var reader=cmd.ExecuteReader()
while(reader.Read()){}
```

注意避免陷阱

```c#
using var outStream = File.OpenWrire("1.txt");
using var writer = new StreamWriter(outStream);
writer.WriteLine("hello");
string s = File.ReadAllText("1.txt");
Console.WriteLine(s);
//写资源还没释放 开始读  报错

// 解决 1. 传统嵌套  2.大括号选择作用域
{
    using var outStream = File.OpenWrire("1.txt");
	using var writer = new StreamWriter(outStream);
	writer.WriteLine("hello");   
}
string s = File.ReadAllText("1.txt");
Console.WriteLine(s)
```

#### 异步编程

异步编程的优点是可以提高服务器接待请求的数量，但不会使得单个请求的处理效率变高，甚至有可能略有降低。

