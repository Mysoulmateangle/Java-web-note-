##### XML
>可扩展标记语言

xml的运用：持久化的存储数据，数据配置，数据交换
#### 解析XML文件
##### xml文件
~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<phones>
	<phone id="1">
		<brand>华为</brand>
		<type>p8</type>
		<price>2000</price>
	</phone>
	<phone id="2">
		<brand>苹果</brand>
		<type>Iphonex</type>
		<price>9000</price>
	</phone>
</phones>
~~~

##### 获取，并查询xmL文件下的内容
~~~ java
import java.io.File;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

public class test {
//获取，并查询xmL文件下的内容
public static void main(String[] args) throws Exception {
  SAXReader reader=new SAXReader();//创建解析器对象
  File file=new File("use.xml");//得到文件相对路径
  Document doc = reader.read(file);//创建一个doucment xml 文档对象
  //对doument 文档对象进行操作
  Element rootElement = doc.getRootElement(); //获取根节点
  List<Element> elements = rootElement.elements();
  System.out.println(rootElement.getName());
  for (Element element : elements) {//用迭代器进行遍历获取子节点 phone,子节点类型为element
    System.out.println(element.getName());
    System.out.println("---------------------------");
    Attribute attribute = element.attribute("id");//获取属性，属性类型是attribute
    String attributeName = attribute.getName();
    String attributeText = attribute.getText();
    System.out.println(attributeName+"---"+attributeText);
    List<Element> childs = element.elements();
    for (Element c : childs) {
      String name = c.getName();//通过getName,getText进行获取子节点的内容
      String text = c.getText();
      System.out.println(name+"---"+text);

    }
    System.out.println("---------------------------");
  }
}

}

~~~
##### 将xml文件进行封装，成对象
~~~ java
import java.io.File;
import java.util.ArrayList;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import com.zhou.bean.phone;

public class test2 {

	//将xml封装成对象，然后用一个list进行输出
	public static void main(String[] args) throws Exception {
		int id;
		String brand="";
		String type="";
		int price=0;
		List<phone> list = new ArrayList<phone>();
		SAXReader reader=new SAXReader();//获取解析器对象
		File file =new File("use.xml");//文档位置
		Document doc = reader.read(file);//获取一个对use.xml文件的doucment的对象
		Element rootElement = doc.getRootElement();//获取根节点
		List<Element> elements = rootElement.elements();//获取子节点
		for (Element element : elements) {
			Attribute attribute = element.attribute("id");//获取子节点的属性
			String attributeName = attribute.getName();//获取id

			String attributeText = attribute.getText();//获取id值
			 id = Integer.parseInt(attributeText);
			List<Element> childs = element.elements();

			for (Element c : childs) {
				String name = c.getName();
				String text = c.getText();
				if (name.equals("brand")) {
					brand=text;//获取值
				}
				else if (name.equals("type")) {
					type=text;
				}
				else if (name.equals("price")) {
					price=Integer.parseInt(text);
				}
			}
			phone phone=new phone(id, brand, type, price);
			list.add(phone);
		}
		for (phone phone : list) {
			System.out.println(phone);
		}
	}

}
------------------------------------------------------------------
<!--对象类-->
package com.zhou.bean;

public class phone {
	private int id;
	private String brand;
	private String type;
	private int price;

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getBrand() {
		return brand;
	}

	public void setBrand(String brand) {
		this.brand = brand;
	}

	public String getType() {
		return type;
	}

	public void setType(String type) {
		this.type = type;
	}

	public int getPrice() {
		return price;
	}

	public void setPrice(int price) {
		this.price = price;
	}

	public phone(int id, String brand, String type, int price) {
		super();
		this.id = id;
		this.brand = brand;
		this.type = type;
		this.price = price;
	}

	@Override
	public String toString() {
		return "phone [id=" + id + ", brand=" + brand + ", type=" + type + ", price=" + price + "]";
	}


}


~~~
##### 当需要对xml修改的时候，需要对doucment进行提交，改变相对路径下xml内文件的内容
~~~ java
//内存中document对象进行修改后，需要在次保存
  OutputFormat format=OutputFormat.createPrettyPrint();
  format.setEncoding("utf-8");
  XMLWriter writer=new XMLWriter(new FileWriter("use.xml"),format);
  writer.write(doc);
  System.out.println("修改成功");
  writer.close()
~~~
##### 修改xml文件中的根节点
~~~ java
import java.io.File;
import java.io.FileWriter;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;

public class test3 {
<!--修改根节点的值-->
	public static void main(String[] args) throws Exception {
		SAXReader reader =new SAXReader();//创建解析器
		File file = new File("use.xml");
		Document doc = reader.read(file);//创建对xml文档进行操作的document对象
		Element rootElement = doc.getRootElement();//获取根节点
		List<Element> elements = rootElement.elements();
		for (Element element : elements) {
			Attribute attribute = element.attribute("id");
			String text = attribute.getText();
			if (text.equals("2")) {
				List<Element> childs = element.elements();
				for (Element child : childs) {
					if (child.getName().equals("price")) {
						child.setText("4500元");
					}
				}
			}
		}
		//内存中document对象进行修改后，需要在次保存
		OutputFormat format=OutputFormat.createPrettyPrint();
		format.setEncoding("utf-8");
		XMLWriter writer=new XMLWriter(new FileWriter("use.xml"),format);
		writer.write(doc);
		System.out.println("修改成功");
		writer.close();
	}

}

~~~
##### 增加节点在xml文件中
~~~   java
import java.io.File;
import java.io.FileWriter;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;

public class test4 {
	//增加节点
	public static void main(String[] args) throws Exception {
		SAXReader reader=new SAXReader();//创建解析器对象
		File file=new File("use.xml");
		Document doc = reader.read(file);//创建一个文档对象
		Element rootElement = doc.getRootElement();//获取根节点
		List<Element> elements = rootElement.elements();//获取一个子节点集合
		for (Element element : elements) {
			Attribute attribute = element.attribute("id");
			String text = attribute.getText();
			if (text.equals("1")) {
				Element child = element.addElement("Storage");
				child.setText("10G");

			}
		}
		OutputFormat format=OutputFormat.createPrettyPrint();
		format.setEncoding("utf-8");
		XMLWriter writer=new XMLWriter(new FileWriter("use.xml"), format);
		writer.write(doc);
		System.out.println("添加成功");
		writer.close();

	}

}

~~~
#### 删除节点
~~~ java
import java.io.File;
import java.util.ArrayList;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import com.zhou.bean.phone;

public class test2 {

	//删除节点（注意父节点删除子节点）
	import java.io.File
	import java.io.FileWriter;
	import java.util.List;

	import org.dom4j.Attribute;
	import org.dom4j.Document;
	import org.dom4j.DocumentException;
	import org.dom4j.Element;
	import org.dom4j.io.OutputFormat;
	import org.dom4j.io.SAXReader;
	import org.dom4j.io.XMLWriter;

	public class test5 {
		//删除元素，
		public static void main(String[] args) throws Exception {
			SAXReader reader=new SAXReader();
			Document document = reader.read("use.xml");
			Element root = document.getRootElement();

			List<Element> elements = root.elements();
			for (Element element : elements) {
				String id = element.attribute("id").getText(); // ID值
				if ("1".equals(id)) {
					List<Element> childs = element.elements();
					for (Element c : childs) {
						String name = c.getName();
						if (name.equals("dada")) {
							element.remove(c);
						}
					}
				}
			}

			OutputFormat format=OutputFormat.createPrettyPrint();
			format.setEncoding("utf-8");
			XMLWriter writer=new XMLWriter(new FileWriter("use.xml"), format);
			writer.write(document);
			System.out.println("删除成功");
			writer.close();
		}

	}


~~~
### dom4dom4jsystem
~~~ java
package com.zhou.utils;

import java.io.File;
import java.io.FileWriter;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;
import org.junit.Test;

import com.zhou.bean.phone;

public class phoneUtils  {


	//xml文件地址
	 static String  xmlAdress="use.xml";

	//查询xml所有数据
	public static void queryall() throws Exception {
		SAXReader reader=new SAXReader();
		Document doc = reader.read(new File(xmlAdress));
		Element rootElement = doc.getRootElement();
		List<Element> elements = rootElement.elements();
		System.out.println(rootElement.getName());
		System.out.println("-------------------------");
		for (Element element : elements) {
			System.out.println(element.getName());
			Attribute attribute = element.attribute("id");
			String attributeName = attribute.getName();
			String attributeText = attribute.getText();
			System.out.println(attributeName+":"+attributeText);
			List<Element> childs = element.elements();
			for (Element child : childs) {
				String name = child.getName();
				String text = child.getText();
				System.out.println(name+":"+text);
				System.out.println("-----------------");
			}
		}
	}
	//获取xml文件，返回一个封装的手机集合
	public static List<phone> getall() throws Exception {
		int id;
		String brand="";
		String type="";
		int price=0;
		List<phone> list = new ArrayList<phone>();
		SAXReader reader=new SAXReader();//获取解析器对象
		File file =new File("use.xml");//文档位置
		Document doc = reader.read(file);//获取一个对use.xml文件的doucment的对象
		Element rootElement = doc.getRootElement();//获取根节点
		List<Element> elements = rootElement.elements();//获取子节点
		for (Element element : elements) {
			Attribute attribute = element.attribute("id");//获取子节点的属性
			String attributeName = attribute.getName();//获取id

			String attributeText = attribute.getText();//获取id值
			 id = Integer.parseInt(attributeText);
			List<Element> childs = element.elements();

			for (Element c : childs) {
				String name = c.getName();
				String text = c.getText();
				if (name.equals("brand")) {
					brand=text;//获取值
				}
				else if (name.equals("type")) {
					type=text;
				}
				else if (name.equals("price")) {
					price=Integer.parseInt(text);
				}
			}
			phone phone=new phone(id, brand, type, price);
			list.add(phone);
		}
		return list;
	}
	//删除节点
	public static int deleChild() throws Exception {
		Scanner scanner=new Scanner(System.in);
		System.out.println("请给出要删除的子节点");
		 String childName = scanner.next();
			System.out.println("请给出要删除的id");
			String id = scanner.next();
		SAXReader reader=new SAXReader();
		Document doc = reader.read(new File(xmlAdress));
		Element rootElement = doc.getRootElement();
		List<Element> elements = rootElement.elements();
		for (Element element : elements) {
			Attribute attribute = element.attribute("id");
			String attributeText = attribute.getText();
			if (id.equals(attributeText)) {
				List<Element> childs = element.elements();
				for (Element child : childs) {
					if (child.getName().equals(childName)) {
						element.remove(child);

						OutputFormat format=OutputFormat.createPrettyPrint();
						format.setEncoding("utf-8");
						XMLWriter writer=new XMLWriter(new FileWriter("use.xml"), format);
						writer.write(doc);
						System.out.println("删除成功");
						writer.close();
						return 1;
					}
				}
			}
		}

		return 0;
	}
	//增加节点

	public static int addChild() throws Exception {
		Scanner scanner=new Scanner(System.in);
		System.out.println("请给出要新增的子节点");
		 String childName = scanner.next();
		 System.out.println("请给出要新增的字节点的值");
		 String childText = scanner.next();
			System.out.println("请给出要新增的id");
			String id = scanner.next();
		SAXReader reader=new SAXReader();
		Document doc = reader.read(new File(xmlAdress));
		Element rootElement = doc.getRootElement();
		List<Element> elements = rootElement.elements();
		for (Element element : elements) {
			Attribute attribute = element.attribute("id");
			String attributeText = attribute.getText();
			if (attributeText.equals(id)) {
				Element addElement = element.addElement(childName);
				addElement.setText(childText);

				OutputFormat format=OutputFormat.createPrettyPrint();
				format.setEncoding("utf-8");
				XMLWriter writer=new XMLWriter(new FileWriter("use.xml"), format);
				writer.write(doc);
				System.out.println("添加成功");
				writer.close();
				return 1;
			}

		}

		return 0;

	}
	//修改节点内容
	public static int changeChild() throws Exception {
		Scanner scanner=new Scanner(System.in);
		System.out.println("请给出要修改的子节点");
		 String childName = scanner.next();
		 System.out.println("请给出要修改的字节点的值");
		 String childText = scanner.next();
			System.out.println("请给出要修改的id");
			String id = scanner.next();
		SAXReader reader=new SAXReader();
		Document doc = reader.read(new File(xmlAdress));
		Element rootElement = doc.getRootElement();
		List<Element> elements = rootElement.elements();
		for (Element element : elements) {
			Attribute attribute = element.attribute("id");
			String attributeText = attribute.getText();
			if (attributeText.equals(id)) {
				List<Element> childs = element.elements();
				for (Element c : childs) {
					if (c.getName().equals(childName)) {
						c.setText(childText);

						OutputFormat format=OutputFormat.createPrettyPrint();
						format.setEncoding("utf-8");
						XMLWriter writer=new XMLWriter(new FileWriter("use.xml"), format);
						writer.write(doc);
						System.out.println("修改成功");
						writer.close();
						return 1;
					}
				}

			}

		}

		return 0;
	}


}
-------------------------------------------------
import java.util.List;
import java.util.Scanner;

import com.zhou.bean.phone;
import com.zhou.utils.phoneUtils;

public class phoneSystem {
	public static void main(String[] args) throws Exception {
		System.out.println("欢迎来到xml解析器功能");
		while (true) {
			System.out.println("请选择要进行的操作：1.查询全部  2.新增手机节点  3.删除手机节点  4.修改手机节点  5.封装成phone对象  6.退出：");
			Scanner scanner = new Scanner(System.in);
			String a = scanner.next();
			int i = Integer.parseInt(a);
			switch (i) {
			case 1:
				phoneUtils.queryall();
				break;
			case 2:
				int addChild = phoneUtils.addChild();
				if (addChild == 0) {
					System.out.println("请重新新增");
				}
				break;
			case 3:
				int deleChild = phoneUtils.deleChild();
				if (deleChild == 0) {
					System.out.println("请重新删除");
				}
				break;
			case 4:
				int changeChild = phoneUtils.changeChild();
				if (changeChild == 0) {
					System.out.println("请重新修改");
				}
				break;
			case 5:
				List<phone> list = phoneUtils.getall();
				System.out.println("是否存入文本");
				break;
				case 6:

			default:
				System.out.println("别他妈乱输");
				break;
			}
		}
	}

}
~~~
