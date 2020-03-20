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
1. 设name中有8个字符，少于8个则补空格(这里用"\u0000")，多于8个则去掉后面多余的部分。   
2. file.skipBytes(n);    // 跳过字节大小为n的数据
3. file.seek(n);      //  将文件指针移动到文件指定位置  
4. file.substring(a,b);  // 返回字符串的子字符串。  
                           a: 原字符串的脚标为a处的字符开始(包括)。(脚标从0开始算)
                        b: 对应脚标为b处的字符(不包括)，即子字符串的最后一个字符为原字符串脚标为b-1处的字符。                            5.(1)getBytes();            // 使用默认的编码将字符串进行编码后存到一个新的byte数组里，并返回该byte数组。
  (2)getBytes(String CharsetName);   // 使用指定的编码将字符串进行编码后存到一个新的byte数组里，并返回该byte数组。
  (3)String s = new String(str.getBytes("utf-8"),"gbk"));  // 将已经解析出来的字节数据转化为gbk编码格式的字符串， 
                                                       在内存中即为gbk格式的字节数组转为unicode格式去交互传递。
  * 无论如论转换，java程序的数据都是要先和Unicode做转换。  
  
## 流类
     Java的流式输入/输出建立在4个抽象类的基础上：InputStream、OutputStream、Reader和Writer。  
  InputStream和OutputStream被设计成字节流类，Reader和Writer被设计成字符流类。一般来说，处理字符或字符串时应使用字符流类，
  处理字节或二进制对象时应使用字节流类。 

## 字节流  
 1. 字节流以InputStream和OutStream为顶层。         
 2.InputStream是一个定义了Java流式字节输入模式的抽象类，该类的所有方法在出错时时都会引发一个IOEException异常。   
 3.OutputStream是定义了流式字节输出模式的抽象类，该类的所有方法返回一个void值并且在出错的情况下会引发一个IOEException异常。     
     
 4.* FileInputStream (文件字节输入流)    
     FileInputStream类创建一个能从文件读取字节的InputStream类，它的两个常用的构造方法如下:    
     # FileInputStream(String filepath)    
     # FileInputStream(File fileObj)    
     示例:  
     InputStream f0 = new FileputStream("c:\\test.txt");    
     
     File f = new File(""c:\\test.txt);   
     InputStream f1 = new FileInputStream(f);    
   
  5.* FileOutStream (文件字节输出流)   
     FileOutputStream类继承类OutputStream类，它的构造方法如下：   
       
     # FileOutStream(String filePath)   
     # FileOutputStream(File fileObj)   
     # FileOutStream(String filePath, boolean append)   
        它们可以引发IOException或SecurityException异常。在这里filepPth是绝对路径，    
      fileObj是描述该文件的File  对象。如果append为true，文件则以设置搜索路径模式打开。       
      FileOutStream的创建不依赖文件是否存在。在创建对象时，FileOutStream会打在开输出文件     
      之前就创建它。 在这种情况下如果打开一个只读文件，则会引发一个IOException异常。      
  
* 向文件中写入字符串并读出   
```  
import java.io.*;

public class StreamDemo
{
    public static void main(String[] args)
    {
        File f= new File("D:\\java\\temp.txt");
        OutputStream out = null;
        try
        {
            // 向上转型
            out = new FileOutputStream(f);
        }
        catch (FileNotFoundException e)
        {
            e.printStackTrace();
        }
        // 将字符串转成字节数组
        byte b[] = "Hello World!!!".getBytes();
        try
        {
            // 将byte数组写入到文件之中
            out.write(b);
        }
        catch (IOException e1)
        {
            e1.printStackTrace();
        }
        try
        {
            out.close();
        }
        catch (IOException e2)
        {
            e2.printStackTrace();
        }

        //  以下为读文件操作
        InputStream in = null;
        try
        {
            in = new FileInputStream(f);
        }
        catch (FileNotFoundException e3)
        {
            e3.printStackTrace();
        }
        // 开辟一个新空间用于接收文件都进来的数据
        byte b1[] = new byte[1024];
        int i = 0;
        try
        {
            // 将b1的引用传递到read()方法之中，同时此方法返回读入数据的个数
            i = in.read(b1);
        }
        catch (IOException e4)
        {
            e4.printStackTrace();
        }
        try
        {
            in.close();
        }
        catch (IOException e5)
        {
            e5.printStackTrace();
        }
        // 将byte数组转换为字符串输出
        System.out.println(new String(b1, 0, i));
    }
}
```
  
* public String(byte[] bytes,int offset,int length)   
    构造一个新的 String，方法是使用指定的字符集解码字节的指定子数组。新的 String 的长度是一个字符集函数，因此不能等于该子数组的长度。   
    当给定字节在给定字符集中无效的情况下，该构造方法无指定的行为。当需要进一步控制解码过程时，应使用 CharsetDecoder 类。    
    参数：   
    bytes - 要解码为字符的字节   
    offset - 要解码的首字节的索引   
    length - 要解码的字节数   
    抛出：   
    IndexOutOfBoundsException - 如果 offset 和 length 参数索引字符超出 bytes 数组的范围。     
     
     
 
 
  
  
  
    

                             
                                                     
