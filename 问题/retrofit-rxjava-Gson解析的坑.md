# retrofit rxjava Gson解析的坑
 最近重构项目，把原有的网络请求改为retrofit+rxjava的。但是遇到了一个不大不小的坑。（ps：后台的锅~~~）   
 一般情况下我们的json是这样的：   

```java 
 {
    "data": {
        "message": "测试内容"
    },
    "err_msg": "",
    "ret_code": 0
}
```
写个基础的Basebean通过继承的方式来处理，把返回码、返回信息和数据分开，像下面这样：<!--more-->   

```java
public class BaseBean {

    protected String err_msg;
    protected int ret_code;
}
```
这种方式当然是不可取的，并且代码的耦合性太高。   
所以这个时候突然想起来retrofit有个GsonConverterFactory类，查阅源码发现重写这个类还是比较简单的就能实现我们想要的自定义的Gson解析。  

首先需要创建一个Converter.Factory的类

```java

public class CustomGsonConverterFactory extends Converter.Factory {

    private final Gson gson;
    private static final MediaType MEDIA_TYPE = MediaType.parse("application/json; charset=UTF-8");
    private static final Charset UTF_8 = Charset.forName("UTF-8");

    public CustomGsonConverterFactory(Gson gson) {
        if (gson == null) throw new NullPointerException("gson == null");
        this.gson = gson;
    }

    public static CustomGsonConverterFactory create() {
        return create(new Gson());
    }

    public static CustomGsonConverterFactory create(Gson gson) {
        return new CustomGsonConverterFactory(gson);
    }

    @Override
    public Converter<ResponseBody, ?> responseBodyConverter(Type type, Annotation[] annotations, Retrofit retrofit) {
        TypeAdapter<?> adapter = gson.getAdapter(TypeToken.get(type));
        return new CustomGsonResponseBodyConverter<>(gson, adapter);
    }

    @Override
    public Converter<?, RequestBody> requestBodyConverter(Type type, Annotation[] parameterAnnotations, Annotation[] methodAnnotations, Retrofit retrofit) {
        return super.requestBodyConverter(type, parameterAnnotations, methodAnnotations, retrofit);
    }

    @Override
    public Converter<?, String> stringConverter(Type type, Annotation[] annotations, Retrofit retrofit) {
        return super.stringConverter(type, annotations, retrofit);
    }
```
responseBodyConverter方法是网络请求成功后，数据返回的处理方法。

```java
@Override
    public Converter<ResponseBody, ?> responseBodyConverter(Type type, Annotation[] annotations, Retrofit retrofit) {
        TypeAdapter<?> adapter = gson.getAdapter(TypeToken.get(type));
        return new CustomGsonResponseBodyConverter<>(gson, adapter);
    }
```
如上方法 我们需要返回一个Converter对象。关键来了我们需要操作的地方就在这个CustomGsonResponseBodyConverter里面.

```java
public class CustomGsonResponseBodyConverter<T> implements Converter<ResponseBody, T> {
        private final Gson gson;
        private final TypeAdapter<T> adapter;

        CustomGsonResponseBodyConverter(Gson gson, TypeAdapter<T> adapter) {
            this.gson = gson;
            this.adapter = adapter;
        }

        /**
         * 这里统一对json数据做了处理
         * 通过gson获取httpstatus对象 判断ret_code码 判断是否有异常
         * 有异常 抛出异常和异常信息
         * 没异常 单独获取data的json字符串 再由okhttp3 转换为我们需要的object
         *
         * @param value
         * @return
         * @throws IOException
         */
        @Override
        public T convert(ResponseBody value) throws IOException {
            //获取json字符串，此处由于API原因 字符串只能获取一次
            String response = value.string();
            //获取httpStatus的数据
            HttpStatusBean result = gson.fromJson(response, HttpStatusBean.class);
            if (result.ret_code == CommonParameter.RET_CODE_SUCCESS) {
                  //此处具体操作原因 看GsonModel
                GsonBean gsonModel = gson.fromJson(response, GsonBean.class);
                String pString = GSonUtil.getJson(gsonModel.data);
                MediaType contentType = value.contentType();
                Charset charset = contentType != null ? contentType.charset(UTF_8) : UTF_8;
                InputStream inputStream = new ByteArrayInputStream(pString.getBytes());
                Reader reader = new InputStreamReader(inputStream, charset);
                JsonReader jsonReader = gson.newJsonReader(reader);
                return adapter.read(jsonReader);
            } else {
                value.close();
                throw new ApiException(result.err_msg);
            }
        }
    }
}
```
实现Converter < ResponseBody, T>接口会实现convert中的方法。方法中的value就是返回的数据，用ResponseBody封装起来的。通过

```java
value.string()
```
获得服务器返回的json的字符串，这样我们就可以为所欲为了。（只获取某一部分呀之类的）   
下面这段代码是把json字符串再重新包装返回：

```java
Charset charset = contentType != null ? contentType.charset(UTF_8) : UTF_8;
                InputStream inputStream = new ByteArrayInputStream(pString.getBytes());
                Reader reader = new InputStreamReader(inputStream, charset);
                JsonReader jsonReader = gson.newJsonReader(reader);
```

这样我们就实现了json数据的自定义。