# Java学习笔记

## 文件IO操作
### File类中的方法
```
import java.io.*;

public class FileDemo 
{
    public static void main(String[] args)
    {
        File f = new File("c:\\1.txt");
        if (f.exists())
            f.delete();
        else
            try
            {
                f.createNewFile();
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        // getName()方法，取得文件名
        System.out.println("文件名：" + f.getName());
        // getPath()方法，取得文件路径
        System.out.println("文件路径：" + f.getPath());
        // getAbsolutePath()方法，得到绝对路径名
        System.out.println("绝对路径：" + f.getAbsolutePath());
        // getParent()方法，得到父文件夹名
        System.out.println("父文件夹名称：" + f.getParent());
        // exists()，判断文件是否存在
        System.out.println(f.canWrite()?"文件存在":"文件不存在");
        // canWrite()，判断文件是否可写
        System.out.println(f.canWrite()?"文件可写":"文件不可写");
        // canRead(),判断文件是否可读
        System.out.println(f.canRead()?"文件可读":"文件不可读");
        // isDirectory()，判断是否是目录
        System.out.println(f.isDirectory()?"是":"不是" + "目录");
        // isFile()，判断是否是文件
        System.out.println(f.isFile()?"是":"不是" + "文件");
        // isAbsolute()，是否是绝对路径名称
        System.out.println(f.isAbsolute()?"是绝对路径":"不是绝对路径");
        // lastModified()，文件最后的修改时间
        System.out.println("文件最修改时间：" + f.lastModified());
        // length()，文件的长度
        System.out.println("文件大小：" + f.length() + "Byte");
    }
} 
```
## RandomAccessFile类
   *RandomAccessFile类支持“随机访问”方式，可以跳转到文件的任意位置处读写数据。*
   ```
   RandomAccessFile类对象的构造方法
   new RandomAccessFile(f, 'rw')      读写方式
   new RandomAccessFile(f, 'r')       只读方式
   当以读写的方式打开一个文件时，如果这个文件不存在，程序会自动创建此文件
   ```
   
   示例
   ```
import java.io.*;
public class RandomFileDemo
{
    public static void main(String[] args) throws Exception
    {
        Employee e1 = new Employee("zhangsan",23 );
        Employee e2 = new Employee("lisi", 24 );
        Employee e3 = new Employee("wangwu", 25);
        RandomAccessFile ra = new RandomAccessFile("D:\\java\\employee.txt", "rw");
        ra.write (e1.name.getBytes());
        ra.writeInt(e1.age);
        ra.write(e2.name.getBytes());
        ra.writeInt(e3.age);
        ra.write(e3.name.getBytes());
        ra.writeInt(e3.age);
        ra.close();

        RandomAccessFile raf = new RandomAccessFile("D:\\java\\employee.txt", "r");
        int len = 8;
        raf.skipBytes(12);              // 跳过第1个员工的信息，其姓名8字节，年龄4字节
        System.out.println("第2个员工信息：");
        String str = "";
        for (int i= 0; i < len; i++)
            str = str + (char)raf.readByte();
        System.out.println("name: " + str);
        System.out.println("age: "+ raf.readInt());
        System.out.println("第1个员工的信息：");
        raf.seek(0);                      // 将文件指针移到到文件开始位置

        str = "";
        for (int i = 0; i < len; i++)
            str = str + (char)raf.readByte();
        System.out.println("name: " + str);
        System.out.println("age: "+ raf.readInt());
        System.out.println("第3个员工的信息：");

        raf.skipBytes(12);            // 跳过第2个员工的信息
        str = "";
        for (int i = 0; i < len; i++)
            str = str + (char)raf.readByte();
        System.out.println("name: " + str.trim());
        System.out.println("age: " + raf.readInt());
        raf.close();
    }
}
class Employee
{
    String name;
    int age;
    final static int LEN = 8;
    public Employee(String name, int age)
    {
        if (name.length() > LEN)
        {
            name = name.substring(0, 8);
        }
        else
        {
            while (name.length() < LEN)
                name = name + "\u0000";
        }
        this.name = name;
        this.age = age;

    }
}
```

*总结*
```
1. 设name中有8个字符，少于8个则补空格(这里用"\u0000")，多于8个则去掉后面多余的部分。
2. file.skipBytes(n);    // 跳过字节大小为n的数据
3. file.seek(n);      //  将文件指针移动到文件指定位置  
4. file.substring(a,b);  // 返回字符串的子字符窜。a: 原字符串的脚标为a处的字符开始(包括)，(0开始算)。b: 脚标为b处的字符(不包括)。

                             
                                                     
