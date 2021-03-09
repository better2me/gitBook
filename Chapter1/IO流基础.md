# 第一篇   IO流基础

### 一、字符流

1. #### FileReader

   ```JAVA
   int read(); //读取单个字符并返回
   int read(char[] cbuf);// 一次读取一个字符数组的数据，返回的是实际读取的字符个数
   ```

2. #### FileWriter

   ```java
   //写一个字符串数据
   void write(String str);
   
   //写一个字符串中的一部分数据, index:开始索引,len:写几个
   void write(String str,int index,int len);
   
   //写一个字符数据,这里写int类型的好处是既可以写char类型的数据，也可以写char对应的int类型的值。如：'a',97
   void write(int ch);
   
   //写一个字符数组数据
   void write(char[] chs);
   
   //写一个字符数组的一部分数据, index:开始索引,len:写几个
   void write(char[] chs,int index,int len);
   ```

3. #### BufferReader

   ```java
   String readLine();//一次读取一行数据，但是不读取换行符
   ```

4. #### BufferWriter.

   ```java
   void newLine();//写一个换行符，这个换行符由系统决定
   ```

5. #### 注意。

   字符流不刷新是写不到文件的。

### 二、转换流。

1. #### OutputStreamWriter 将字节输出流转为字符输出流 

   - ##### 构造方法：

     ```java
     public OutputStreamWriter(OutputStream out)  //将指定的字节流转为字符流
     
     public OutputStreamWriter(OutputStream out, String charsetName)    
     ```

   - ##### 转化流的作用：

     - ​	用于方法的调用,形参是字符流，但是当你的选择只有字节流时，用该方法则实现转换

     - ​	用于解决文件的编码问题

2. #### InputStreamReader 将字节输入流转为字符输入流 

   - ##### 构造方法：

     ```java
     public InputStreamReader(InputStream in)
       
     public InputStreamReader(InputStream in， String  charset); 
     
     //写的时候使用什么编码，读取的时候也用什么编码，否则会发生错误。
     ```

### 三、字节流

1. #### OutputStream 字节输出流 

   OutputStream类是抽象类，是字节流输出的超类。实现类如：**FileOutputStream**

   - ##### 构造方法

     ```java
     FileOutputStream(File file,boolean append); //是否追加
     
     FileOutputStream(String name,boolean append); //是否追加
     ```

   - ##### 普通方法

     ```java
     //写出整个字节数组。
     void write(byte[] b);
     
     //写出字节数组的一部分
     void write(byte[] b,int off,int len);
     
     //写出指定字节
     void write(int b);
     ```

   - ##### 例子

     ```java
     public static void main(String[] args) throws Exception {
         File file = new File("c:\\file.txt");
         FileOutputStream fos = new FileOutputStream(file, true);
         String str = "\r\n"+"itcast";
         fos.write(str.getBytes());
         fos.close();
     }
     ```

2. #### InputStream 字节输入流。

   InputStream类是抽象类，是字节输入流的超类。实现类如：**FileInputStream**

   - #### 构造方法

     ```java
     FileInputStream(File file);
     
     FileInputStream(String name);
     ```

   - #### 普通方法

     ```java
     // 读取一个字节。
     read();
     
     // 读取字节数组。
     read(byte[] b);
     ```

   - #### 例子

     ```java
     public static void main(String[] args) throws IOException {
     
         File file = new File("c:\\file.txt");
         // 创建一个字节输入流对象,必须明确数据源，其实就是创建字节读取流和数据源相关联。
         FileInputStream fis = new FileInputStream(file);
         //创建一个字节数组。
         byte[] buf = new byte[1024];//长度可以定义成1024的整数倍。
         int len = 0;
         while((len=fis.read(buf))!=-1){
         	System.out.println(new String(buf,0,len));
         }
         fis.close();
     }
     ```

### 四、 组合使用输入输出

```java
public static void main(String[] args) throws IOException {
    File srcFile = new File("c:\\YesDir\test.JPG");
    File destFile = new File("copyTest.JPG");

    // 明确字节流 输入流和源相关联，输出流和目的关联。
    FileInputStream fis = new FileInputStream(srcFile);
    FileOutputStream fos = new FileOutputStream(destFile);
    
  	//定义一个缓冲区。
    byte[] buf = new byte[1024];
    int len = 0;
    while ((len = fis.read(buf)) != -1) {
    	fos.write(buf, 0, len);// 将数组中的指定长度的数据写入到输出流中。
    }
    
  	// 关闭资源。
    fos.close();
    fis.close();
}
```

### 五、字节输出缓冲流

```java
private static void  write() throws IOException {
  
    //创建基本的字节输出流
    FileOutputStream fileOut =  new FileOutputStream("abc.txt");
    //使用高效的流，把基本的流进行封装，实现速度的提升
    BufferedOutputStream out = new BufferedOutputStream(fileOut);
    //写数据
    out.write("hello".getBytes());
    //关闭流
    out.close();
  
}
```

### 六、字节输入缓冲流。

```java
private static void  read()  throws  IOException {
    //1,创建缓冲流对象
    FileInputStream fileIn =  new  FileInputStream("abc.txt");
    //把基本的流包装成高效的流
    BufferedInputStream in =  new  BufferedInputStream(fileIn);
    //2，读数据
    int ch = -1;
    while  ( (ch = in.read()) != -1 ) {
      //打印
      System.out.print((char)ch);
    }
    //3，关闭
    in.close();
}
```

### 七、案例 复制单级文件夹。

```java
public static void  main(String[] args)  throws IOException {

    // 封装目录
    File srcFolder =  new  File("e:\\demo");
    // 封装目的地
    File destFolder =  new  File("e:\\test");
    // 如果目的地文件夹不存在，就创建
    if (!destFolder.exists()) {
    	destFolder.mkdir();
    }

    // 获取该目录下的所有文本的File数组
    File[] fileArray = srcFolder.listFiles();
    // 遍历该File数组，得到每一个File对象
    for (File file : fileArray) {
      // System.out.println(file);
      // 数据源：e:\\demo\\e.mp3
      // 目的地：e:\\test\\e.mp3
      String name = file.getName(); // e.mp3
      File newFile =  new  File(destFolder, name); // e:\\test\\e.mp3
      copyFile(file, newFile);
    }

}

private static void  copyFile(File file, File newFile)  throws  IOException {
    BufferedInputStream bis =  new  BufferedInputStream(new  FileInputStream(file));
    BufferedOutputStream bos =  new  BufferedOutputStream(new  FileOutputStream(newFile));

    byte [] bys =  new byte [1024];
    int  len = 0;
    while  ((len = bis.read(bys)) != -1) {
    	bos.write(bys, 0, len);
    }

    bos.close();
    bis.close();
}
```

### 八、对象序列化流 ObjectOutputStream

1. #### 构造方法

   ```
   ObjectOutputStream(OutputStream out);
   ```

2. #### 普通方法

   ```java
   void  writeObject(Object obj );
   //将指定对象写入流。
   ```

3. #### 例子

   ```java
   //明确存储对象的文件。
   FileOutputStream fos = new FileOutputStream("tempfile\\obj.object");
   
   //给操作文件对象加入写入对象功能。
   ObjectOutputStream oos = new ObjectOutputStream(fos);
   
   //调用了写入对象的方法。
   oos.writeObject(new Person("wangcai",20));
   
   //关闭资源。
   oos.close();
   ```

### 九、对象反序列化流 ObjectInputStream

1. #### 构造方法。

   ```
   ObjectInputStream ( InputStream in) ;
   ```

2. #### 普通方法。

   ```java
   Object readObject();
   //从流上读取对象到内存。
   ```

3. #### 例子

   ```java
   //1,定义流对象关联存储了对象文件。
   FileInputStream fis = new FileInputStream("tempfile\\obj.object");
   
   //2,建立用于读取对象的功能对象。
   ObjectInputStream ois = new ObjectInputStream(fis);
   Person obj = (Person)ois.readObject();
   System.out.println(obj.toString());
   ```

### 十、序列化接口

​	当一个对象要能被序列化，这个对象所属的类必须实现Serializable接口。否则会发生NotSerializableException异常。同时当反序列化对象时，如果对象所属的class文件在序列化之后进行的修改，那么进行反序列化也会发生异常InvalidClassException。

### 十一、 瞬态关键字  transient

​	某个类的属性 不需要被序列化时 ， 这个属性可以用关键字 transient 修饰，同时静态修饰的属性也不会被序列化。因为序列化把对象属性进行持久化，而静态的属性属于类加载时的数据，所以不会被序列化。

```java
private static String name;

private transient int age;
```

### 十二、打印流

1. #### 字节打印流  PrintStream.

2. #### 字符打印流  PrintWriter.

3. #### 方法

   ```java
   void print(String str);
   
   void println(Sting str);
   ```

4. #### 例子

   ```java
   //创建流
   //PrintWriter out = new PrintWriter(new FileWriter("printFile.txt"));
   PrintWriter out = new PrintWriter("printFile.txt");
   
   //写数据
   for(int i=0; i<5; i++) {
   	out.println("helloWorld");
   }
   
   //关闭流
   out.close();
   ```