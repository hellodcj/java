##property的使用
```
		Properties p = new Properties();
		File file = new File(fileName);
		p.load(new FileInputStream(file));
		for (String name :p.stringPropertyNames()){
			objectPool.put(name,createObj(p.getProperty(name)));
		}
```
这里面有个小技巧，关于文件的读取，如果不想使用绝对路径来得到文件，那么可以使用`System.getProperty("user.dir")`
```
private String fileName =System.getProperty("user.dir")+ "\\src\\com\\dcj\\core\\basictype\\obj.txt";
```
