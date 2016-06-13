## for Android: Access Local Xml | 读取本地xml文件数据

本地XML文件可以放在应用根目录assets文件夹、res/xml、res/raw、SDcard卡、应用的data目录等。
除了res/xml下的xml文件可用getXml(int id)获取XML文档，返回一个解析器对象XmlResourceParer外，
其它位置均是获取XML文档，并返回一个Inputstream对象，再进行数据读取。获取方法分别如下

### 在assets目录下
```java
InputStream inputStream = getResources().getAssets().open(fileName);
```
示例
```java
private InputStream getXMLStreamFromAssets(String fileName) {
    InputStream stream = null;
    try {
        stream = getResources().getAssets().open(fileName);
    } catch (Exception e) {
        Log.d(TAG, "except()->data: " + e.toString());
    }
    try{
        XmlPullParser pullParser = Xml.newPullParser();
        //设置数据源编码
        pullParser.setInput(stream, "utf-8");
        int type = pullParser.getEventType();
        StringBuilder sb  = new StringBuilder();
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
            type = pullParser.next();
        }
        Log.d(TAG, "getXMLStreamFromAssets()->data: " + sb.toString());
    }catch (Exception e){
        Log.d(TAG, "Exception()->data parse: " + e.toString());
    }
    return stream;
}
```
### 在res/xml目录下（推荐使用）
```java
XmlResourceParser resourceParser = getResources().getXml(R.xml.book);
```
示例
```java
private void getXMLFromRes() {
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
            //下一条记录
            resourceParser.next();
        }
        Log.d(TAG, "getXMLFromRes()->data: " + sb.toString());
    } catch (Exception e) {
    }
}
```
### 在res/raw目录下
```java
InputStream inputStream = getResources().openRawResource(R.raw.book2);
```
示例
```java
private InputStream getXMLFromRaw(){
    InputStream stream = null;
    try{
        stream = getResources().openRawResource(R.raw.book2);
    }catch (Exception e){
        Log.d(TAG, "Exception()->data: " + e.toString());
    }
    try{
        XmlPullParser pullParser = Xml.newPullParser();
        //设置数据源编码
        pullParser.setInput(stream, "utf-8");
        int type = pullParser.getEventType();
        StringBuilder sb  = new StringBuilder();
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
            type = pullParser.next();
        }
        Log.d(TAG, "getXMLFromRaw()->data: " + sb.toString());
    }catch (Exception e){
        Log.d(TAG, "Exception()->data parse: " + e.toString());
    }
    return  stream;
}
```
### 在SDcard卡里
```java
private InputStream getInputStreamFromSDcard(String fileName){
    InputStream stream = null;
    try {
        // 判断是否存在SD卡
        if (Environment.getExternalStorageState().equals(
                Environment.MEDIA_MOUNTED)) {
            // 获取SD卡的目录
            String path = Environment.getExternalStorageDirectory().toString();
            File xmlFlie = new File(path + "/temp_xml", fileName);
            stream = new FileInputStream(xmlFlie);
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
    return stream;
}
```
