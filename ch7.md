# Java 8 API ʾ�����ַ�������ֵ���������ļ�

> ԭ�ģ�[Java 8 API by Example: Strings, Numbers, Math and Files](http://winterbe.com/posts/2015/03/25/java8-examples-string-number-math-files/) 

> ���ߣ�[����](https://github.com/wizardforcel)  

> Э�飺[CC BY-NC-SA 4.0](http://creativecommons.org/licenses/by-nc-sa/4.0/)

�����Ľ̳̺����¶��漰��Java8������Ҫ�ĸı䣬����[lambda���ʽ](ch1.md)��[����ʽ������](ch2.md)�����Ǵ�������ִ������[JDK 8 API](http://winterbe.com/posts/2014/03/29/jdk8-api-explorer/)��Ҳ�����Ľ�������һЩʵ�õ����Ժͷ�����

��ƪ�̳��漰��Java 8 API�е���ЩС�޸� -- ÿ����ʹ�ü��׶��Ĵ���ʾ���������������Ǻúÿ�һ���ַ�������ֵ���������ļ���

## �����ַ���

�����µķ��������ַ�������ʹ�ã�`join`��`chars`����һ������ʹ��ָ���ķָ��������κ��������ַ�������Ϊһ���ַ�����

```java
String.join(":", "foobar", "foo", "bar");
// => foobar:foo:bar
```

�ڶ�������`chars`���ַ��������ַ��������������������������Щ�ַ���ʹ����ʽ������

```java
"foobar:foo:bar"
    .chars()
    .distinct()
    .mapToObj(c -> String.valueOf((char)c))
    .sorted()
    .collect(Collectors.joining());
// => :abfor
```

���������ַ�����������ʽģʽ��Ҳ�������������������ǿ��Էָ��κ�ģʽ�������������������������ǣ������ǽ��ַ����ָ�Ϊ�����ַ�����������������������

```java
Pattern.compile(":")
    .splitAsStream("foobar:foo:bar")
    .filter(s -> s.contains("bar"))
    .sorted()
    .collect(Collectors.joining(":"));
// => bar:foobar
```

���⣬����ģʽ������ת��Ϊν�ʡ���Щν�ʿ����������������ڹ����ַ�������

```java
Pattern pattern = Pattern.compile(".*@gmail\\.com");
Stream.of("bob@gmail.com", "alice@hotmail.com")
    .filter(pattern.asPredicate())
    .count();
// => 1
```

�����ģʽ�������κ���`@gmail.com`��β���ַ���������֮������Java8��`Predicate`�����˵����ʼ���ַ����

## ������ֵ

Java8����˶��޷������Ķ���֧�֡�Java�е���ֵ�����з��ŵģ����磬���������۲�`Integer`��

`int`�ɱ�ʾ���`2 ** 32`������Java�е���ֵĬ��Ϊ�з��ŵģ��������һ�����������ֱ�ʾ���ţ�0Ϊ������1Ϊ�����������Դ�ʮ���Ƶ�0��ʼ�������з���������Ϊ`2 ** 31 - 1`��

�����ͨ��`Integer.MAX_VALUE`����������

```java
System.out.println(Integer.MAX_VALUE);      // 2147483647
System.out.println(Integer.MAX_VALUE + 1);  // -2147483648
```

Java8����˽����޷���������֧�֣������ǿ�������ι�����

```java
long maxUnsignedInt = (1l << 32) - 1;
String string = String.valueOf(maxUnsignedInt);
int unsignedInt = Integer.parseUnsignedInt(string, 10);
String string2 = Integer.toUnsignedString(unsignedInt, 10);
```

�����㿴�������������ڿ��Խ������޷�����`2 ** 32 - 1`����Ϊ������������Ҳ���Խ������ֵת�����޷��������ַ�����ʾ��

����֮ǰ������ʹ��`parseInt`��ɣ������������չʾ��������

```java
try {
    Integer.parseInt(string, 10);
}
catch (NumberFormatException e) {
    System.err.println("could not parse signed int of " + maxUnsignedInt);
}
```

�����ֵ���ɽ���Ϊ�з�����������Ϊ�����������Χ`2 ** 31 - 1`��

## ��������

`Math`������������һЩ������������ֵ���������ʲô��˼�أ������Ѿ�������������ֵ���Ͷ������ֵ�����Ե���������Ľ�����ܱ����Ĵ�Сװ��ʱ���ᷢ��ʲô�أ�

```java
System.out.println(Integer.MAX_VALUE);      // 2147483647
System.out.println(Integer.MAX_VALUE + 1);  // -2147483648
```

�����㿴���������������������������ͨ�������ǲ�Ը�⿴���ġ�

Java8������ϸ���ѧ�����֧�������������⡣`Math`��չ��һЩ����������ȫ����`exact`��β������`addExact`�������������ܱ���ֵ����װ��ʱ����Щ����ͨ���׳�`ArithmeticException`�쳣������ش��������

```java
try {
    Math.addExact(Integer.MAX_VALUE, 1);
}
catch (ArithmeticException e) {
    System.err.println(e.getMessage());
    // => integer overflow
}
```

������ͨ��`toIntExact`��������ת��Ϊ����ʱ�����ܻ��׳�ͬ�����쳣��

```java
try {
    Math.toIntExact(Long.MAX_VALUE);
}
catch (ArithmeticException e) {
    System.err.println(e.getMessage());
    // => integer overflow
}
```

## �����ļ�

`Files`�������״���Java7�����룬��ΪNIO��һ���֡�JDK8 API�����һЩ����ķ��������ǿ��Խ��ļ����ں���ʽ������������������̽��һЩ����ʾ����

### �г��ļ�

`Files.list`������ָ��Ŀ¼������·��ת��Ϊ�������������������ļ�ϵͳ��������ʹ������`filter`��`sorted`����������

```java
try (Stream<Path> stream = Files.list(Paths.get(""))) {
    String joined = stream
        .map(String::valueOf)
        .filter(path -> !path.startsWith("."))
        .sorted()
        .collect(Collectors.joining("; "));
    System.out.println("List: " + joined);
}
```

����������г��˵�ǰ����Ŀ¼�������ļ���֮��ÿ��·����ӳ��Ϊ�����ַ�����ʾ��֮���������ˡ������������Ϊһ���ַ���������㻹����Ϥ����ʽ����������Ӧ���Ķ��ҵ�[Java8�������̳�](ch2.md)��

������Ѿ�ע�⵽���������Ĵ�����װ��`try-with`����С�������ʵ����`AutoCloseable`����������������Ҫ��ʽ�ر�����������Ϊ������IO������

> ���ص���������`DirectoryStream`�ķ�װ�������Ҫ��ʱ�����ļ���Դ����Ӧ��ʹ��`try-with`�ṹ��ȷ������ʽ������ɺ���������`close`���������á�

### �����ļ�

�����������ʾ����β�����Ŀ¼������Ŀ¼�µ��ļ���

```java
Path start = Paths.get("");
int maxDepth = 5;
try (Stream<Path> stream = Files.find(start, maxDepth, (path, attr) ->
        String.valueOf(path).endsWith(".js"))) {
    String joined = stream
        .sorted()
        .map(String::valueOf)
        .collect(Collectors.joining("; "));
    System.out.println("Found: " + joined);
}
```

`find`������������������Ŀ¼·��`start`����ʼ�㣬`maxDepth`���������������ȡ�������������һ��ƥ��ν�ʣ��������������߼�������������У���������������JavaScirpt�ļ�����`.js`��β���ļ�������

���ǿ���ʹ��`Files.walk`�����������ͬ����Ϊ��������������ÿ���ļ���������Ҫ��������ν�ʡ�

```java
Path start = Paths.get("");
int maxDepth = 5;
try (Stream<Path> stream = Files.walk(start, maxDepth)) {
    String joined = stream
        .map(String::valueOf)
        .filter(path -> path.endsWith(".js"))
        .sorted()
        .collect(Collectors.joining("; "));
    System.out.println("walk(): " + joined);
}
```

��������У�����ʹ������ʽ����`filter`����ɺ��ϸ�������ͬ����Ϊ��

### ��д�ļ�

���ı��ļ������ڴ棬�Լ����ı��ļ�д���ַ�����Java 8 ���Ǽ򵥵����񡣲���Ҫ��ȥ��Ū��д���ˡ�`Files.readAllLines`��ָ�����ļ��������ж����ַ����б��С�����Լ򵥵��޸�����б����ҽ���ͨ��`Files.write`д����һ���ļ��У�

```java
List<String> lines = Files.readAllLines(Paths.get("res/nashorn1.js"));
lines.add("print('foobar');");
Files.write(Paths.get("res/nashorn1-modified.js"), lines);
```

Ҫע����Щ�������ڴ沢��ʮ�ָ�Ч����Ϊ�����ļ���������ڴ档�ļ�Խ�����õĶ���Ҳ��Խ��

�����ʹ��`Files.lines`��������Ϊ�ڴ��Ч����������������ȡÿһ�У���ʹ�ú���ʽ��������������ʽ����������һ���԰������ж������ڴ档

```java
try (Stream<String> stream = Files.lines(Paths.get("res/nashorn1.js"))) {
    stream
        .filter(line -> line.contains("print"))
        .map(String::trim)
        .forEach(System.out::println);
}
```

�������Ҫ����ľ�ϸ���ƣ�����Ҫ����һ���µ�`BufferedReader`�����棺

```java
Path path = Paths.get("res/nashorn1.js");
try (BufferedReader reader = Files.newBufferedReader(path)) {
    System.out.println(reader.readLine());
}
```

���ߣ�����Ҫд���ļ�ʱ���򵥵ع���һ��`BufferedWriter`�����棺

```java
Path path = Paths.get("res/output.js");
try (BufferedWriter writer = Files.newBufferedWriter(path)) {
    writer.write("print('Hello World');");
}
```

`BufferedReader`Ҳ���Է��ʺ���ʽ��������`lines`�����������������湹����������

```java
Path path = Paths.get("res/nashorn1.js");
try (BufferedReader reader = Files.newBufferedReader(path)) {
    long countPrints = reader
        .lines()
        .filter(line -> line.contains("print"))
        .count();
    System.out.println(countPrints);
}
```

ĿǰΪֹ����Կ���Java8�ṩ�������򵥵ķ�������ȡ�ı��ļ���ÿһ�У�ʹ�ļ�������ӱ�ݡ�

���ҵ�������Ҫ��ʽʹ��`try-with`������ر��ļ��������ʹʾ��������Щ���ҡ����ڴ�����ʽ�����������ڵ�������`count`��`collect`ʱ�����Զ��رգ���Ϊ�㲻������ͬ�������ϵ�����ֹ�������Ρ�

��ϣ������ϲ����ƪ���¡�����ʾ�����붼�й���[Github](https://github.com/winterbe/java8-tutorial)�ϣ�������Դ���Ҳ�������[Java8����](http://winterbe.com/java/)�Ĵ����Ĵ���Ƭ�Ρ������ƪ���¶���������������[�ղ�](https://github.com/winterbe/java8-tutorial)�ҵĲֿ⣬������Twitter��[��ע��](https://twitter.com/winterbe_)��

���ֱ�̣�
