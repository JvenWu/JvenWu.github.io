## for Android: Xml Parser | Xml 解析
XML解析三种方式

1.DOM  
通用性强，它会将XML文件的所有内容读取到内存中，然后允许您使用DOM API遍历XML树、检索所需的数据，简单直观，但需要将文档读取到内存，并不太适合移动设备

2.SAX  
SAX是一个解析速度快并且占用内存少的xml解析器，采用事件驱动，它并不需要解析整个文档  
实现：继承DefaultHandler，覆写startElement、endElement、characters等方法

3.PULL  
Android自带的XML解析器，和SAX基本类似，也是事件驱动，不同的是PULL事件返回的是数值型，推荐使用
### DOM解析
>DOM解析常用类型
- Document    文档对象
- NodeList    节点集合
- Node        节点
- Element     元素

要解析的xml文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<books>
    <book price = "20">
        <name>Wawawa 1</name>
    </book>
    <book price = "21">
        <name>Wawawa 2</name>
    </book>
</books>
```
解析主代码
```java
//从sdCard读入文件流
InputStream stream = getInputStreamFromSDcard("temp_book.xml");
// 创建DOM工厂对象
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
try {
    // 获得DocumentBuilder对象
    DocumentBuilder builder = factory.newDocumentBuilder();
    // 获取文档对象
    Document document = builder.parse(stream);
    // 获取文档对象的root元素
    Element root = document.getDocumentElement();
    //获得root元素下的所有book节点
    NodeList bookNodes = root.getElementsByTagName("book");
    // 遍历所有的book节点
    for (int i = 0; i < bookNodes.getLength(); i++) {
        // 根据item(index)获取该索引对应的节点对象
        Element bookNode = (Element) bookNodes.item(i);
        // 获取price属性值
        String price = bookNode.getAttribute("price");
        Log.d(TAG, "run()->book price: " + price);
        // 获取该节点下面的所有子节点
        NodeList bookChildNodes = bookNode.getChildNodes();
        // 遍历book子节点
        for (int index = 0; index < bookChildNodes.getLength(); index++) {
            // 获取子节点
            Node node = bookChildNodes.item(index);
            // 判断node节点是否是元素节点
            if (node.getNodeType() == Node.ELEMENT_NODE) {
                //把节点转换成元素节点
                Element element = (Element) node;
                //判断元素节点是否是name元素节点
                if ("name".equals(element.getNodeName())) {
                    //获得节点值
                    String bookName = element.getFirstChild().getNodeValue();
                    Log.d(TAG, "run()->book name: " + bookName);
                }
            }
        }
    }
    stream.close();
} catch (Exception e) {
}
```
### SAX解析
>SAX解析常用事件
- startDocument() 读到文档开始时触发事件
- endDocument() 读到文档结束时触发事件
- startElement() 读到标签开始时触发事件
- endElement() 读到标签结束时触发事件
- characters() 读到文档字符数据时触发事件，空字符，也会触发这个事件

SAX解析器
```java
public class IotHandler extends DefaultHandler {
    private static final String TAG = "IotHandler";
    public static final String strUrl = "http://blog.csdn.net/liudehuaii18/rss/list";
    private boolean isBook = false;
    private Context mContext;
    public void processFeed(Context context){
        this.mContext = context;
        try{
            //实例解析工厂
            SAXParserFactory factory = SAXParserFactory.newInstance();
            //获得解析类
            SAXParser parser = factory.newSAXParser();
            //获得reader
            XMLReader reader = parser.getXMLReader();
            //设置解析器
            reader.setContentHandler(this);
            InputStream stream = new URL(strUrl).openStream();
            //InputStream stream = getXMLFromRaw();
            //开始解析
            reader.parse(new InputSource(stream));
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    /**
     * 通过url获取图片
     * @param url 图片地址
     * @return bitmap图片
     */
    private Bitmap getBitmap(String url) {
        try {
            HttpURLConnection connection =
                    (HttpURLConnection)new URL(url).openConnection();
            connection.setDoInput(true);
            connection.connect();
            InputStream input = connection.getInputStream();
            Bitmap bitmap = BitmapFactory.decodeStream(input);
            input.close();
            return bitmap;
        } catch (Exception e) { return null; }
    }
    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        super.startElement(uri, localName, qName, attributes);
        //当前是否为book标签
        if(localName.equals("book")){
            isBook = true;
            //获得属性值
            String price = attributes.getValue("price");
            Log.d(TAG,"startElement()->localname: " + localName + " attr: " + price);
        }
    }
    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        //将字符数组转成String值
        String str = new String(ch).substring(start,start + length);
        if(isBook){
            Log.d(TAG,"characters()->str: " + str);
            isBook = false;
        }
    }
    /**
    * 获取位于res/raw下的book2.xml文件流
    * @return 文件流
    */
    private InputStream getXMLFromRaw(){
        InputStream stream = null;
        try{
            stream = mContext.getResources().openRawResource(R.raw.book2);
        }catch (Exception e){
            Log.d(TAG, "Exception()->data: " + e.toString());
        }
        return  stream;
    }
}
```
### PULL解析
PULL是根据不同的事件触发来解析的

>常用的事件类型有
- START_DOCUMENT
- END_DOCUMENT
- START_TAG
- END_TAG

通过getEventType()可获得解析事件
next()或nextText()可移动到下一解析事件，并返回解析事件类型

要解析的xml文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<books>
    <book price = "20">Hahaha</book>
    <book price = "21">Wawawa</book>
</books>
```

使用XmlPullParser类的解析方式：
```java
new Thread(new Runnable() {
    @Override
    public void run() {
          getXMLFromRes();
        InputStream stream = null;
        //从sdCard读取要解析的文件流
        stream = getInputStreamFromSDcard("book.xml");
        try{
            XmlPullParser pullParser = Xml.newPullParser();
            //设置数据源编码
            pullParser.setInput(stream, "utf-8");
            int type = pullParser.getEventType();
            StringBuilder sb  = new StringBuilder();
            //是否读取到文件尾
            while (type != XmlPullParser.END_DOCUMENT) {
                switch (type) {
                    case XmlPullParser.START_TAG:
                        if(pullParser.getName().equals("book")){
                            //获取属性值
                            String bookPrice = pullParser.getAttributeValue(null, "price");
                            sb.append("price:" + bookPrice + ",");
                            //获得文本值
                            String bookName = pullParser.nextText();
                            sb.append("book name:" + bookName);
                        }
                        sb.append("\n");
                        break;
                    case XmlPullParser.END_TAG:
                        break;
                }
                //触发下一事件
                type = pullParser.next();
            }
        }catch (Exception e){
        }
    }
}).start();
```
使用XmlResourceParser类的解析方式
```java
//从res/xml/book.xml中读取
XmlResourceParser resourceParser = getResources().getXml(R.xml.book);
try {
    StringBuilder sb = new StringBuilder();
    //是否读取到文件尾
    while (resourceParser.getEventType() != XmlResourceParser.END_DOCUMENT) {
        //是否是开始标签
        if (resourceParser.getEventType() == XmlResourceParser.START_TAG) {
            //获取标签名称
            String tagName = resourceParser.getName();
            if (tagName.equals("book")) {
                //获取属性值
                String bookPrice = resourceParser.getAttributeValue(null, "price");
                sb.append("price:" + bookPrice + ",");
                //获得文本值
                String bookName = resourceParser.nextText();
                sb.append("book name:" + bookName);
            }
            sb.append("\n");
        }
        //触发下一事件
        resourceParser.next();
    }
    Log.d(TAG, "getXMLFromResXml()->data: " + sb.toString());
} catch (Exception e) {
}
```
