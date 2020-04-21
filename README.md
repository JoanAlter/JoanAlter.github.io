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
 
   RandomAccessFile类对象的构造方法
   new RandomAccessFile(f, 'rw')      读写方式
   new RandomAccessFile(f, 'r')       只读方式
   当以读写的方式打开一个文件时，如果这个文件不存在，程序会自动创建此文件
     
* 示例 *   
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

* 总结     
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
    
 ## 字符流  
 * 1.Reader
    Reader是定义Java的流式字符输入模式的抽象类，该类的所有方法在出错的情况下都将引发IOException异常。  
    
 * 2.Writer  
     Wruter是定义流式字符输出的抽象类，所有该类的方法都返回一个void值并在出错的条件下引发IOException异常。  
     
  * 3.FileREader  
      FileReader类创建一个可以读取文件内容的类Reader类。常用构造方法如下:  
      * FileReader(String filePath)  filePath为完整路径。  
      * FileReader(File Obj)  
      
  * 4.FileWriter  
      FileWriter类创建一个可以读取文件内容的Reader类。常用构造方法如下:  
      * FileWriter(String filePath)  
      * FileWriter(String filePath, bollean append)  // 当append=true,相当于原来文件里写的A现在要写B追加的话文件就变成了A->AB。  append=false,就是覆盖，覆盖的话就是A->B。
      * FileWriter(File fileObj)  
    
  * 向文件中写入字符串并读出  
  ```
import java.io.*;

public class CharDemo
{
    public static void main(String args[])
    {
        File f = new File("D:\\java\\temp.txt");
        Writer out = null;
        try
        {
            out = new FileWriter(f);
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        // 声明一个String类型对象
        String str = "Hello World!";
        try
        {
           // 将str内容写入到文件中
           out.write(str);
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

        // 以下为读文件操作
        Reader in = null;
        try
        {
            in = new FileReader(f);
        }
        catch (IOException e3)
        {
            e3.printStackTrace();
        }
        // 开辟一个空间用于接收文件都进来的数据
        char c1[] = new char[1024];
        int i = 0;
        try
        {
            // 将c1的引用传递到read()方法之中，提示此方法返回读入数据个数        
            i = in.read(c1);
            // 将in流中的数据读取出来，并依次存储到数组c1中，返回值为实际读取的有效数据的个数。
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
        // 将字符数组转换为字符串输出
        System.out.println(new String(c1, 0, i));
    }
}  
```  
* 注意：可将305-312行代码注释掉，FileWrriter类并不是继承自Writer类，而是继承了Writer的子类(OutputStreamWriter)，    
        此类为字节流和字符流的转换类。  
* 结论：字符流用到了缓冲区，而字节流没有用到缓冲区。另外也可以用Writer类中的flush()方法强制清空缓冲区。  


## 管道流  
   * 管道流主要用于连接两个线程间的通信。管道流也分为字节流(PiedInputStream, piedOutputStream)与字符流
(PiedReader, PiedWriter)。  
   * 一个PiedInputStream对象必须和一个PiedOutputStream对象进行连接而产生一个通信管道，PiedOutputStream可以向管道  
中写入数据，PiedInputStream可以从管道中读取PiedOutputStream写入的数据。  


```  
import java.io.*;

public class PipeStreamDemo
{
    public static void main(String[] args)
    {
        try
        {
            Sender sender = new Sender();       // 产生两个线程对象
            Receiver receiver = new Receiver();
            PipedOutputStream out = sender.getOutputStream();  // 写入
            PipedInputStream in = receiver.getInputStream();  // 读出
            out.connect(in);
            sender.start();
            receiver.start();
        }
        catch (IOException e)
        {
            System.out.println(e.getMessage());
        }
    }
}
class Sender extends Thread
{
    private  PipedOutputStream out = new PipedOutputStream();
    public PipedOutputStream getOutputStream()
    {
        return out;
    }

    public void run()
    {
        String s = new String("Receiver, 你好！");
        try
        {
            out.write(s.getBytes());
            out.close();
        }
        catch (IOException e)
        {
            System.out.println(e.getMessage());
        }
    }
}
class Receiver extends Thread
{
    private PipedInputStream in = new PipedInputStream();
    public PipedInputStream getInputStream()
    {
        return in;
    }
    public void run()
    {
        String s = null;
        byte[] buf = new byte[1024];
        try
        {
            int len = in.read(buf);
            s = new String (buf, 0, len);
            System.out.println("收到了以下信息：" +  s);
        }
        catch (IOException e)
        {
            System.out.println(e.getMessage());
        }
    }
}

```  

* ByteArrayInputStream与ByteArrayOutputStream  
  ByteArrayInputStream是输入流的一种实现，它有两个构造方法，每个构造方法都需要一个字节数组来作为其数据源。  
  ``` 
  ByteArrayInputStream(byte[] buf)  
  ByteArrayInputStream(byte[] buf, int offse, int length)  
  ByteArrayOutStream()  
  ByteArrayOutStream(int)  
  ```   
  
 示例
 ```
import java.io.*;

public class ByteArrayDemo
{
    public static void main(String[] args) throws Exception
    {
        String tmp = "abcdefghijklmnopqrstuvwxyz";
        byte[] src = tmp.getBytes();                    // src为转换前的内存块
        ByteArrayInputStream input = new ByteArrayInputStream(src);
        ByteArrayOutputStream output = new ByteArrayOutputStream();
        new ByteArrayDemo().transform(input, output);
        byte[] result = output.toByteArray();           // result为转换后的内存块
        System.out.println(new String(result));
    }
    public void transform(InputStream in, OutputStream out)
    {
        int c = 0;
        try
        {
            while ((c = in.read()) != -1)       // read在读到流的结尾处返回-1
            {
                int C = (int)Character.toUpperCase((char)c);
                out.write(C);
            }
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }
}

```

* PrintWriter  
PrintWriter(OutputStream)  
PrintWriter(OutputStream, boolean)  
PrintWriter(Writer)  
PrintWriter(Writer, boolean)  


* DataInputStream与DataOutputStream  
  DataInputStream与DataOutputStream提供了与平台无关的数据操作，通常回先通过DataOutputStream按照一定的格式输出，   
再通过DataInputStream按照一定格式读入。由于可以得到java的各种基本类型甚至字符串，这样对得到的数据便可以方便地进  
行处理，在这通过协议传输的信息的网络上是非常适用的。

示例  
```
import java.awt.image.DataBufferInt;
import java.io.*;

public class DataStreamDemo
{
    public static void main(String[] args) throws IOException
    {
        // 将数据写入某一种载体
        DataOutputStream out = new DataOutputStream(new FileOutputStream("D:\\java\\order.txt"));
        // 价格
        double[] prices = {18.99, 9.22, 5.22, 4.21};
        // 数目
        int[] units = {10, 10, 20, 39, 40};
        // 产品名称
        String[] descs = {"T恤衫", "杯子", "洋娃娃", "大头针", "钥匙链"};

        // 想数据过滤流写入主要类型
        for (int i = 0; i < prices.length; i++)
        {

            // 写入价格，使用tab隔开数据
            out.writeDouble(prices[i]);
            out.writeChar('\t');
            // 写入数目
            out.writeInt(units[i]);
            out.writeChar('\t');
            // 写入产品名称，行尾加入换行符
            out.writeChars(descs[i]);
            out.writeChar('\n');
        }
        out.close();

        // 将数据读出
        DataInputStream in = new DataInputStream(new FileInputStream("D:\\java\\order.txt"));
        double price;
        int unit = 0;
        StringBuffer desc;
        double total = 0.0;

        try
        {
            // 当文本被全部读出以后会抛出EoFException异常，中断循环
            while (true)
            {
                // 读出价格
                price = in.readDouble();
                // 跳过tab
                in.readChar();
                // 读出数目
                in.readInt();
                // 跳过tab
                in.readChar();
                char chr;
                // 读出产品名称
                desc = new StringBuffer();
                while ((chr = in.readChar()) != '\n')
                {
                    desc.append(chr);
                }
                System.out.println("订单信息：" + "产品名称：" + desc + "，\t数量：" + unit + "，\t价格："
                                   + price);
                total = total + unit * price;
            }
        }
        catch (EOFException e)
        {
            System.out.println("\n总共需要：" + total + "元");
        }
        in.close();
    }
}


```


* 文件合并
```
package FileIO;
import java.io.*;

public class SequenceDemo
{
    public static void main(String[] args) throws IOException
    {
        File f1 = new File("D:\\java\\1.txt");
        File f2 = new File("D:\\java\\2.txt");
        String a = "the first";
        String b = "the second";
        byte[] a1 = a.getBytes();
        byte[] b1 = b.getBytes();

        try
        {
           OutputStream F1 = new FileOutputStream(f1);
           OutputStream F2 = new FileOutputStream(f2);
           F1.write(a1);
           F2.write(b1);
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }

        // 声明两个文件读入流
        FileInputStream in1 = null, in2 = null;
        // 声明一个序列流
        SequenceInputStream s = null;
        FileOutputStream out = null;

        try
        {
            // 构造两个被读入的文件
            File inputFile1 = new File("D:\\java\\1.txt");
            File inputFile2 = new File("D:\\java\\2.txt");
            // 构造一个输出文件
            File outputFile = new File("D:\\java\\12.txt");

            in1 = new FileInputStream(inputFile1);
            in2 = new FileInputStream(inputFile2);

            // 将两个输入流合为一个输入流
            s = new SequenceInputStream(in1, in2);
            out = new FileOutputStream(outputFile);

            int c;
            while ((c = s.read()) != -1)
            {
                out.write(c);
            }
            in1.close();
            in2.close();
            s.close();
            System.out.println("ok...");
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        finally
        {
            if (in1 != null)
            {
                try
                {
                    in1.close();
                }
                catch (IOException e)
                {
                }
            }
            if (in2 != null)
            {
                try
                {
                    in1.close();
                }
                catch (IOException e)
                {
                }
            }
            if (s != null)
            {
                try
                {
                    s.close();
                }
                catch (IOException e)
                {
                }
                if (out != null)
                {
                    try
                    {
                        out.close();
                    }
                    catch (IOException e)
                    {
                    }
                }
            }
        }

    }
}

```  


* 字节流与字符流的转换  
  InputStreamReader用于将一个字节流中的字节解码城字符，OutputStreamWriter用于将写入的字符编码成字节后写入一个字节流。

构造方法：  
InputStreamReader(InputStream in)  
InputStreamReader(InputStream in, String CharseName)  // 接收已指定字符集名的字符串，并用该字符集创建对象。  
OutputStreamWriter(OutStream in)  
OutputStreamWriter(OutStream in, String CharsetName)  // 接收已指定字符集名的字符串，并用该字符集创建OutStreamWriter对象。  

  为避免频繁地进行字符与字节间的相互转换，最好不要直接使用这两个类进行读写，而应尽量使用BufferedWriter类包装OutStreamWriter类，  
用BufferedReader类包装InputStreamReader类。  
BufferedWriter Out = new BufferedWriter(new OutputStreamWriter(System.out));  
BufferedReader in = new BufferedReader(new InputStreamReader(System.in));  
String strLine = in.readLine();  

* 示例
```
import java.io.*;

public class BufferDemo
{
    public static void main(String[] args) throws IOException
    {
        BufferedReader buf = null;
        buf = new BufferedReader(new InputStreamReader((System.in)));
        String str = null;
        while (true)
        {
            System.out.print("请输入数字：");
            try
            {
                str = buf.readLine();
            }
            catch (IOException e)
            {
                e.printStackTrace();
            }
            int i = -1;
            try
            {
                i = Integer.parseInt(str);
                i++;
                System.out.println("输入的数字修改后为：" + i);
                break;
            }
            catch (Exception e)
            {
                System.out.println("输入的内容不正确，清重新输入！");
            }

        }

    }
}

//  parseInt() 方法用于将字符串参数作为有符号的十进制整数进行解析。
// parseInt(String s, int radix) radix为指定基数(基数可以是 10, 2, 8, 或 16 等进制数)。  

```
  




    
                                                                     
