当用Gson解析泛型会报以下错误：
> com.google.gson.internal.LinkedTreeMap cannot be cast to

解决办法：

```
private Class<T> className;
public List<T> fromJsonList(String json) {
    List<T> mList = new ArrayList<T>();
    Gson gson = new Gson();
    JsonArray array = new JsonParser().parse(json).getAsJsonArray();
    log.debug("className: " + className);
    for(final JsonElement elem : array){
        mList.add(gson.fromJson(elem, className));
    }
    return mList;
}
```