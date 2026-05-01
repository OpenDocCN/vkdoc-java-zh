# 标签

标签现在也用于区分具有相同名称和类型但标签不同的指标。它们可用于在 REST 端点上支持多个 `result_code` 指标，以统计（不）成功调用的次数：

```
@Inject
@Metric(tags="{code,200}", name="result_code")
Counter result_code_200k;

@Inject
@Metric(tags="{code,500}", name="result_code")
Counter result_code_500;

@GET
@Path("/")
public String getData(String someParam) {

 String result = getSomeData(someParam);
 if (result == null ) {
   result_code_500.inc();
 } else {
   result_code_200.inc();
 }
 return result;
}
```

在底层，指标不再仅通过名称和类型来标识，还通过它们的标签来标识。为此，引入了新的 `MetricID` 来承载名称和标签。

